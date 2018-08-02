---
title: Azure 上的 Kubernetes - 叢集自動調整程式
description: 了解如何使用叢集自動調整程式與 Azure Kubernetes Service (AKS) 來根據需求自動縮放叢集。
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186708"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Azure Kubernetes Service (AKS) 上的叢集自動調整程式 - 預覽

Azure Kubernetes Service (AKS) 會提供可在 Azure 中部署受控 Kubernetes 叢集的靈活解決方案。 當資源需求增加時，叢集自動調整程式會根據您設定的條件，讓叢集擴大到符合該需求的大小。 叢集自動調整程式 (CA) 執行此作業的方式是，根據擱置中的 Pod 來縮放代理程式節點。 它會定期掃描叢集，以檢查擱置中的 Pod 或空白節點，然後在允許的情況下增加大小。 根據預設，CA 會每 10 秒掃描一次擱置中的 Pod，並將超過 10 分鐘沒用到的節點移除。 與水平 Pod 自動調整程式 (HPA) 搭配使用時，HPA 會根據需求更新 Pod 複本和資源。 如果沒有足夠的節點或非必要節點可配合此 Pod 縮放，則 CA 會回應並在一組新的節點上排定 Pod。

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) 叢集自動調整程式整合目前為**預覽**狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>

## <a name="prerequisites"></a>必要條件

本文件假設您有已啟用 RBAC 的 AKS 叢集。 如果您需要 AKS 叢集，請參閱 [Azure Kubernetes Service (AKS) 快速入門][aks-quick-start]。

 若要使用叢集自動調整程式，您的叢集必須使用 Kubernetes v1.10.X 或更高版本，而且必須啟用 RBAC。 若要升級您的叢集，請參閱[升級 AKS 叢集][aks-upgrade]上的文章。

## <a name="gather-information"></a>收集資訊

下列清單顯示必須在自動調整程式定義中提供的所有資訊。

- 訂用帳戶識別碼：對應到此叢集所用訂用帳戶的識別碼
- 資源群組名稱：叢集所屬的資源群組名稱 
- 叢集名稱：叢集的名稱
- 用戶端識別碼：權限產生步驟授與的應用程式識別碼
- 用戶端密碼：權限產生步驟授與的應用程式祕密
- 租用戶識別碼：租用戶 (帳戶擁有者) 的識別碼
- 節點資源群組：包含叢集中代理程式節點的資源群組名稱
- 節點集區名稱：您要縮放的節點集區名稱
- 最小節點數目：可存在於叢集中的最小節點數目
- 最大節點數目：可存在於叢集中的最大節點數目
- VM 類型：用來產生 Kubernetes 叢集的服務

使用下列命令取得您的訂用帳戶識別碼： 

``` azurecli
az account show --query id
```

執行下列命令來產生一組 Azure 認證：

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

應用程式識別碼、密碼及租用戶識別碼將會是後續步驟中的 clientID、clientSecret 和 tenantID。

執行下列命令可取得您節點集區的名稱。 

```console
$ kubectl get nodes --show-labels
```

輸出：

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

然後，擷取 **agentpool** 標籤的值。 叢集的預設節點集區名稱是 "nodepool1"。

若要取得節點資源群組的名稱，請擷取 **kubernetes.azure.com<span></span>/cluster** 標籤的值。 節點資源群組名稱的格式通常是 MC_[resource-group]\_[cluster-name]_[location]。

vmType 參數會參考正在使用的服務，在這裡指的是 AKS。

現在，您應該會有下列資訊：

- SubscriptionID
- ResourceGroup
- ClusterName
- ClientID
- ClientSecret
- TenantID
- NodeResourceGroup
- VMType

接下來，將所有這些值編碼為 base64。 例如，若要將 VMType 值編碼為 base64：

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>建立祕密
透過這項資料，您可以使用在先前步驟中找到的值，以下列格式建立部署的秘密：

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

## <a name="create-a-deployment-chart"></a>建立部署圖表

建立名為 `aks-cluster-autoscaler.yaml` 的檔案，然後將下列 YAML 程式碼複製到其中。

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

複製並貼上先前步驟中建立的秘密，然後將其插入檔案的開頭。

接下來，若要設定節點的範圍，請以 MIN:MAX:NODE_POOL_NAME 的形式填寫 `command` 底下的 `--nodes` 引數。 例如：`--nodes=3:10:nodepool1` 就是將最小節點數目設為 3，將最大節點數目設為 10，以及將節點集區名稱設為 nodepool1。

然後，在 **containers** 下的 [映像] 欄位中，填入要使用的叢集自動調整程式版本。 AKS 必須是 v1.2.2 版或更新版本。 此範例使用的是 v1.2.2 版。

## <a name="deployment"></a>部署

執行下列命令來部署叢集自動調整程式

```console
kubectl create -f cluster-autoscaler-containerservice.yaml
```

若要檢查叢集自動調整程式是否正在執行，請使用下列命令並檢查 Pod 清單。 如果有前面加上 "cluster-autoscaler" 的 Pod 正在執行，表示您的叢集自動調整程式已部署成功。

```console
kubectl -n kube-system get pods
```

若要檢視叢集自動調整程式的狀態，請執行

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="next-steps"></a>後續步驟

若要搭配使用叢集自動調整程式與水平 Pod 自動調整程式，請參閱[縮放 Kubernetes 應用程式和基礎結構][aks-tutorial-scale]。

使用 AKS 教學課程深入了解部署和管理 AKS。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
