---
title: 在 Azure Kubernetes Service (AKS) 上使用 GPU
description: 了解如何在 Azure Kubernetes Service (AKS) 上使用 GPU 處理高效能計算或大量圖形的工作負載
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 150eaa6a4df558ed0c737d99cbcc8010baf63e96
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758237"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 上使用 GPU 處理計算密集型工作負載

圖形處理單元 (GPU) 通常用來處理計算密集型工作負載 (例如圖形和視覺效果工作負載)。 AKS 可讓您建立已啟用 GPU 的節點集區，以便在 Kubernetes 中執行這些計算密集型工作負載。 若要深入了解已啟用 GPU 的可用 VM，請參閱 [Azure 中的 GPU 最佳化 VM 大小][gpu-skus]。 針對 AKS 節點，我們建議使用最小的大小：Standard_NC6。

> [!NOTE]
> 已啟用 GPU 的 VM 包含特定硬體，該特定硬體受限於較高的定價和區域可用性。 如需詳細資訊，請參閱[定價][azure-pricing]工具和[區域可用性][azure-availability]。

## <a name="before-you-begin"></a>開始之前

本文假設您的現有 AKS 叢集具有支援 GPU 的節點。 您的 AKS 叢集必須執行 Kubernetes 1.10 或更新版本。 如果您需要符合這些需求的 AKS 叢集，請參閱本文的第一節：[建立 AKS 叢集](#create-an-aks-cluster)。

您也需要 Azure CLI 2.0.59 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

如果您需要符合最低需求的 AKS 叢集 (已啟用 GPU 的節點和使用 Kubernetes 1.10 或更新版本)，請完成下列步驟。 如果您已經有符合這些需求，AKS 叢集[跳至下一節](#confirm-that-gpus-are-schedulable)。

首先，使用 [az group create][az-group-create] 命令來建立 叢集的資源群組。 下列範例會在 eastus 地區建立名為 myResourceGroup 的資源群組：

```azurecli
az group create --name myResourceGroup --location eastus
```

現在，使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。 下列範例會建立大小的單一節點叢集`Standard_NC6`，並執行 Kubernetes 版本 1.11.7:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.11.8
```

使用 [az aks get-credentials][az-aks-get-credentials] 命令取得 AKS 叢集的認證：

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>確認 GPU 可進行排程

建好 AKS 叢集後，請確認 GPU 可在 Kubernetes 中進行排程。 首先，使用 [kubectl get nodes][kubectl-get] 命令列出您叢集中的節點：

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   6m    v1.11.7
```

現在，使用 [kubectl describe node][kubectl-describe] 命令確認 GPU 可進行排程。 在 [容量] 區段下，GPU 應顯示為 `nvidia.com/gpu:  1`。 如果看不到 GPU，請參閱[對 GPU 可用性進行疑難排解](#troubleshoot-gpu-availability)一節。

下列精簡範例顯示名為 aks-nodepool1-18821093-0 的節點上有 GPU：

```
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713780Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5916m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             52368500Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 9148b74152374d049a68436ac59ee7c7
 System UUID:                D599728C-96F3-B941-BC79-E0B70453609C
 Boot ID:                    a2a6dbc3-6090-4f54-a2b7-7b4a209dffaf
 Kernel Version:             4.15.0-1037-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.11.7
 Kube-Proxy Version:         v1.11.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  gpu-resources              nvidia-device-plugin-97zfc               0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m4s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>執行已啟用 GPU 的工作負載

若要查看運作中的 GPU，請使用適當的資源要求對已啟用 GPU 的工作負載進行排程。 在此範例中，我們要針對 [MNIST 資料集](http://yann.lecun.com/exdb/mnist/)執行 [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) 作業。

建立名為 samples-tf-mnist-demo.yaml 的檔案，並貼上下列 YAML 資訊清單。 下列作業資訊清單包含 `nvidia.com/gpu: 1` 的資源限制：

> [!NOTE]
> 如果您在呼叫至驅動程式時，收到版本不符的錯誤 (例如，CUDA 驅動程式版本並不足以執行 CUDA 執行階段版本)，請檢閱 nVidia 驅動程式矩陣相容性圖表 - [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

使用 [kubectl apply][kubectl-apply] 命令來執行作業。 此命令會剖析資訊清單檔，並建立已定義的 Kubernetes 物件：

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>檢視已啟用 GPU 的工作負載狀態和輸出

使用 [kubectl get jobs][kubectl-get] 命令和 `--watch` 引數監視作業進度。 這可能需要幾分鐘來執行第一次的影像提取和資料集處理。 當「完成」資料行顯示「1/1」時，表示作業成功完成：

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

若要查看已啟用 GPU 的工作負載輸出，請先使用 [kubectl get pods][kubectl-get] 命令取得 Pod 的名稱：

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

現在，使用 [kubectl logs][kubectl-logs] 命令來檢視 Pod 記錄。 下列 Pod 記錄範例會確認已探索到適當的 GPU 裝置 `Tesla K80`。 提供自有 Pod 的名稱：

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-02-28 23:47:34.749013: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-02-28 23:47:34.879877: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 3130:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2019-02-28 23:47:34.879915: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 3130:00:00.0, compute capability: 3.7)
2019-02-28 23:47:39.492532: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>清除資源

若要移除本文中建立的相關 Kubernetes 物件，請使用 [kubectl delete job][kubectl delete] 命令，如下所示：

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>對 GPU 可用性進行疑難排解

如果您沒有在節點上看到可用的 GPU，您可能需要部署 DaemonSet 來使用 nVidia 裝置外掛程式。 此 DaemonSet 會在每個節點上執行 Pod，為 GPU 提供必要的驅動程式。

首先，使用 [kubectl create namespace][kubectl-create] 命令建立命名空間，例如 gpu-resources：

```console
kubectl create namespace gpu-resources
```

建立名為 nvidia-device-plugin-ds.yaml 的檔案，並貼上下列 YAML 資訊清單。 更新資訊清單中間的 `image: nvidia/k8s-device-plugin:1.11`，以符合您的 Kubernetes 版本。 例如，如果您的 AKS 叢集中執行 Kubernetes 1.12 版，更新的標記`image: nvidia/k8s-device-plugin:1.12`。

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      containers:
      - image: nvidia/k8s-device-plugin:1.11 # Update this tag to match your Kubernetes version
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

現在，使用 [kubectl apply][kubectl-apply] 命令來建立 DaemonSet：

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

再次執行 [kubectl describe node][kubectl-describe] 命令，以確認 GPU 現在可以在節點上使用。

## <a name="next-steps"></a>後續步驟

若要執行 Apache Spark 作業，請參閱[在 AKS 上執行 Apache Spark 作業][aks-spark]。

如需有關在 Kubernetes 上執行機器學習 (ML) 工作負載的詳細資訊，請參閱 [Kubeflow 實驗室][kubeflow-labs]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
