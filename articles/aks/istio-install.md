---
title: 在 Azure Kubernetes Service (AKS) 中安裝 Istio
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝和使用 Istio 以建立服務網格
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: d85b830b63e2d52f3eeb5df8645edccfccf43c76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465337"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中安裝和使用 Istio

[Istio][istio-github] 是一種開放原始碼服務網格，可在 Kubernetes 叢集中提供跨微服務的主要功能集。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需 Istio 的詳細資訊，請參閱官方文件[什麼是 Istio？][istio-docs-concepts]。

本文說明如何安裝 Istio。 Istio `istioctl` 用戶端二進位檔會安裝到用戶端電腦上，然後 Istio 元件會安裝到 AKS 上的 Kubernetes 叢集中。 這些指示參照的是 Istio 1.0.4 版。 您可以在 [GitHub - Istio 版本][istio-github-releases]找到其他 Istio 版本。

在本文中，您將了解：

> [!div class="checklist"]
> * 下載 Istio
> * 安裝 Istio 用戶端二進位檔
> * 安裝 Istio Kubernetes 元件
> * 驗證安裝

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已建立 AKS 叢集 (已啟用 RBAC 的 Kubernetes 1.10 和更新版本)，並已建立與叢集的 `kubectl` 連線。 如果您需要前述任何方面的協助，請參閱 [AKS 快速入門][aks-quickstart]。

若要安裝 Istio，您需要在叢集中正確安裝並設定 [Helm][helm] 2.10.0 版或更新版本。 如果您需要如何安裝 Helm 的說明，請參閱 [AKS Helm 安裝指引][helm-install]。 如果您未安裝 2.10.0 版以上的 Helm，請予以升級，或參閱 [Istio - Helm 安裝指南][istio-install-helm]以了解其他安裝選項。

本文將 Istio 安裝指引分成數個獨立的步驟。 這些步驟各自都會有說明，因此您可以了解如何安裝 Istio，並了解 Istio 如何與 Kubernetes 搭配運作。 最終結果的結構與官方的 Istio 安裝[指引][istio-install-k8s-quickstart]相同。

## <a name="download-istio"></a>下載 Istio

首先，下載並解壓縮最新的 Istio 版本。 MacOS、Linux 或适用于 Linux 的 Windows 子系统上的 bash shell 的步骤和 PowerShell shell 的步骤稍有不同。 請選擇下列其中一個適用於您慣用環境的安裝步驟：

* [MacOS、Linux 或適用於 Linux 的 Windows 子系統上的 Bash](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

在 MacOS 上，使用 `curl` 下載最新的 Istio 版本，然後使用 `tar` 解壓縮，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

在 Linux 或適用於 Linux 的 Windows 子系統上，使用 `curl` 下載最新的 Istio 版本，然後使用 `tar` 解壓縮，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

在 PowerShell 中，使用 [Invoke-WebRequest][Invoke-WebRequest] 下載最新的 Istio 版本，然後使用 [Expand-Archive][Expand-Archive] 解壓縮，如下所示：

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>安裝 Istio 用戶端二進位檔

`istioctl` 用戶端二進位檔會在用戶端電腦上執行，並可讓您管理 Istio 路由規則和原則。 同樣地，各個用戶端作業系統之間的安裝步驟稍有不同。 請選擇下列其中一個適用於您慣用環境的安裝步驟。

> [!IMPORTANT]
> 确保从已经下载并提取的 Istio 版本的顶层文件夹运行此部分的步骤。

### <a name="macos"></a>MacOS

若要在 MacOS 上的 Bash 型殼層中安裝 Istio `istioctl` 用戶端二進位檔，請使用下列命令。 這些命令會將 `istioctl` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

如果您想要讓 Istio `istioctl` 用戶端二進位檔完成命令列，請進行如下所示的設定：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

現在進入[安裝 Istio Kubernetes 元件](#install-the-istio-kubernetes-components)的章節。

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux 或適用於 Linux 的 Windows 子系統

使用下列命令，在 Linux 或[適用於 Linux 的 Windows 子系統][install-wsl]上的 Bash 型殼層中，安裝 Istio `istioctl` 用戶端二進位檔。 這些命令會將 `istioctl` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

如果您想要讓 Istio `istioctl` 用戶端二進位檔完成命令列，請進行如下所示的設定：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

現在進入[安裝 Istio Kubernetes 元件](#install-the-istio-kubernetes-components)的章節。

### <a name="windows"></a> Windows

若要在 Windows 上的 PowerShell 型殼層中安裝 Istio `istioctl` 用戶端二進位檔，請使用下列命令。 這些命令會將 `istioctl` 用戶端二進位檔複製到新的使用者程式位置，並透過 `PATH` 讓其可供使用。

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>安裝 Istio Kubernetes 元件

> [!IMPORTANT]
> 确保从已经下载并提取的 Istio 版本的顶层文件夹运行此部分的步骤。

> [!NOTE]
> Istio Helm 图表的 `1.0.6` 及更新版本存在中断性变更。 如果选择安装此版本，则需手动创建 Kiali 的机密。 如果已设置 `grafana.security.enabled=true`，则还需手动创建 Grafana 的机密。 请参阅 Istio Helm 图表 [README.md](https://github.com/istio/istio/tree/master/install/kubernetes/helm/istio#installing-the-chart)，更详细地了解如何创建这些机密。

若要將 Istio 元件安裝到 AKS 叢集，請使用 Helm。 將 Istio 資源安裝到 `istio-system` 命名空間，然後啟用關於安全性和監視的其他選項，如下所示：

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Helm 圖表會部署大量物件。 視叢集環境而定，部署作業可能需要 2 到 3 分鐘才會完成。

## <a name="validate-the-installation"></a>驗證安裝

若要確定您已成功部署 Istio，請驗證安裝成果。

先確認是否已建立預期的服務。 使用 [kubectl get svc][kubectl-get] 命令來檢視執行中的服務。 查詢 istio-system 命名空間，其中，Istio 和附加元件已由 Helm 圖表安裝：

```console
kubectl get svc --namespace istio-system --output wide
```

下列範例輸出會顯示現在應該正在執行的服務：

- istio-* 服務
- *jaeger-**、tracing 和 zipkin 附加元件追蹤服務
- prometheus 附加元件計量服務
- grafana 附加元件分析和監視儀表板服務
- kiali 附加元件服務網格儀表板服務

如果 `istio-ingressgateway` 顯示外部 IP 是 `<pending>`，請等候幾分鐘，直到 Azure 網路指派了 IP 位址。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

接著，確認是否已建立所需的 Pod。 使用 [kubectl get pods][kubectl-get] 命令，然後再次查詢 istio-system 命名空間：

```console
kubectl get pods --namespace istio-system
```

下列範例輸出會顯示正在執行的 Pod：

- istio-* Pod
- prometheus-* 附加元件計量 Pod
- grafana-* 附加元件分析和監視儀表板 Pod
- kiali 附加元件服務網格儀表板 Pod

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

所有 Pod 會顯示狀態為 `Running`。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 Pod 回報發生問題，請使用 [kubectl describe pod][kubectl-describe] 命令檢閱其輸出和狀態。

## <a name="accessing-the-add-ons"></a>存取附加元件

上方的設定中安裝了 Istio 和許多附加元件以提供額外功能。 附加元件的使用者介面不會透過外部 IP 位址來公開。 若要存取附加元件使用者介面，請使用 [kubectl port-forward][kubectl-port-forward] 命令。 此命令會在用戶端電腦上的本機連接埠與 AKS 叢集中的相關 Pod 之間建立安全連線。

### <a name="grafana"></a>Grafana

Istio 的分析和監視儀表板會由 [Grafana][grafana] 來提供。 請將用戶端電腦上的本機連接埠 3000 轉送至 AKS 叢集中執行 Grafana 的 Pod 上的連接埠 3000：

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

下列範例輸出會顯示要為 Grafana 設定的連接埠轉送：

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

您現在可以在用戶端電腦上的下列 URL 連線至 Grafana - [http://localhost:3000](http://localhost:3000)。

### <a name="prometheus"></a>Prometheus

計量的運算式瀏覽器會由 [Prometheus][prometheus] 來提供。 請將用戶端電腦上的本機連接埠 9090 轉送至 AKS 叢集中執行 Prometheus 的 Pod 上的連接埠 9090：

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

下列範例輸出會顯示要為 Prometheus 設定的連接埠轉送：

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

您現在可以在用戶端電腦上的下列 URL 連線至 Prometheus 運算式瀏覽器 - [http://localhost:9090](http://localhost:9090)。

### <a name="jaeger"></a>Jaeger

追蹤使用者介面會由 [Jaeger][jaeger] 來提供。 請將用戶端電腦上的本機連接埠 16686 轉送至 AKS 叢集中執行 Jaeger 的 Pod 上的連接埠 16686：

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

下列範例輸出會顯示要為 Jaeger 設定的連接埠轉送：

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

您現在可以在用戶端電腦上的下列 URL 連線至 Jaeger 追蹤使用者介面 - [http://localhost:16686](http://localhost:16686)。

### <a name="kiali"></a>Kiali

服務網格可檢視性儀表板會由 [Kiali][kiali] 來提供。 請將用戶端電腦上的本機連接埠 20001 轉送至 AKS 叢集中執行 Kiali 的 Pod 上的連接埠 20001：

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

下列範例輸出會顯示要為 Kiali 設定的連接埠轉送：

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

您現在可以在用戶端電腦上的下列 URL 連線至 Kiali 服務網格可檢視性儀表板 - [http://localhost:20001](http://localhost:20001)。

Kiali 儀表板的預設使用者名稱和密碼是 username:admin/password:admin。您可以透過 kiali.dashboard.username 和 kiali.dashboard.passphrase Helm 值設定這些認證。

## <a name="next-steps"></a>後續步驟

若要了解如何使用 Istio，以在多個應用程式版本之間提供智慧型路由以及推出 Canary 版本，請參閱下列文件：

> [!div class="nextstepaction"]
> [AKS Istio 智慧型路由案例][istio-scenario-routing]

若要深入探索 Istio 的安裝和組態選項，請參閱下列官方 Istio 文章：

- [Istio - Kubernetes 安裝快速入門][istio-install-k8s-quickstart]
- [Istio - Helm 安裝指南][istio-install-helm]
- [Istio - Helm 安裝選項][istio-install-helm-options]

您也可以使用 [Istio Bookinfo 應用程式範例][istio-bookinfo-example]來跟隨其他案例。

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
