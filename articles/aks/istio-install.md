---
title: 在 Azure Kubernetes Service (AKS) 中安裝 Istio
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝和使用 Istio 以建立服務網格
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 33d86ab8c88b45c7787620773f0df6e7fe888cf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65850404"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中安裝和使用 Istio

[Istio][istio-github] 是一種開放原始碼服務網格，可在 Kubernetes 叢集中提供跨微服務的主要功能集。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需 Istio 的詳細資訊，請參閱官方文件[什麼是 Istio？][istio-docs-concepts]。

本文說明如何安裝 Istio。 Istio`istioctl`到您的用戶端電腦上安裝用戶端二進位和 Istio 元件安裝在 AKS 上在 Kubernetes 叢集中。

> [!NOTE]
> 這些指示參考 Istio 版本`1.1.3`。
>
> Istio`1.1.x`版本都已經過測試針對 Kubernetes 版本 Istio team `1.11`， `1.12`， `1.13`。 您可以找到其他 Istio 版本[GitHub-Istio 釋放][ istio-github-releases]以及每個版本的相關資訊[Istio-版本資訊][ istio-release-notes].

在本文中，您將了解：

> [!div class="checklist"]
> * 下載 Istio
> * 安裝二進位 Istio istioctl 用戶端
> * 在 AKS 上安裝 Istio Crd
> * 在 AKS 上安裝 Istio 元件
> * 驗證 Istio 安裝
> * 存取附加元件
> * 從 AKS 解除安裝 Istio

## <a name="before-you-begin"></a>開始之前

這篇文章中詳述的步驟假設您已建立 AKS 叢集 (Kubernetes`1.11`和更新版本，使用 RBAC 啟用)，並已建立`kubectl`與叢集的連線。 如果您需要前述任何方面的協助，請參閱 [AKS 快速入門][aks-quickstart]。

您將需要[Helm] [ helm]遵循這些指示，並安裝 Istio。 建議您有版本`2.12.2`或更新版本正確安裝並設定您的叢集中。 如果您需要安裝 Helm 的協助，則會看到[AKS Helm 安裝的指導][helm-install]。 所有 Istio pod，則也必須都排程在 Linux 節點上執行。

本文將 Istio 安裝指引分成數個獨立的步驟。 最終結果的結構與官方的 Istio 安裝[指引][istio-install-helm]相同。

## <a name="download-istio"></a>下載 Istio

首先，下載並解壓縮最新的 Istio 版本。 步驟適用於 MacOS、 Linux 或適用於 Linux，和 PowerShell 殼層的 Windows 子系統上的 bash 殼層稍有不同。 選擇其中一個符合您慣用的環境下安裝步驟：

* [MacOS、Linux 或適用於 Linux 的 Windows 子系統上的 Bash](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

在 MacOS 上，使用 `curl` 下載最新的 Istio 版本，然後使用 `tar` 解壓縮，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

在 Linux 或適用於 Linux 的 Windows 子系統上，使用 `curl` 下載最新的 Istio 版本，然後使用 `tar` 解壓縮，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

現在進入一節[安裝 Istio istioctl 用戶端二進位](#install-the-istio-istioctl-client-binary)。

### <a name="powershell"></a>PowerShell

在 PowerShell 中，使用`Invoke-WebRequest`下載最新的 istio 並再擷取`Expand-Archive`，如下所示：

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

現在進入一節[安裝 Istio istioctl 用戶端二進位](#install-the-istio-istioctl-client-binary)。

## <a name="install-the-istio-istioctl-client-binary"></a>安裝二進位 Istio istioctl 用戶端

> [!IMPORTANT]
> 請確定在此區段中，執行步驟，從您下載並解壓縮的 Istio 版次的最上層資料夾。

`istioctl`用戶端電腦上執行二進位的用戶端，並可讓您使用 Istio 服務網格互動。 安裝步驟會稍有不同的用戶端作業系統。 選擇其中一個符合您慣用的環境下安裝步驟：

* [MacOS](#macos)
* [Linux 或適用於 Linux 的 Windows 子系統](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

若要在 MacOS 上的 Bash 型殼層中安裝 Istio `istioctl` 用戶端二進位檔，請使用下列命令。 這些命令會將 `istioctl` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

現在移至下一節[安裝在 AKS 上的 Istio Crd](#install-the-istio-crds-on-aks)。

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux 或適用於 Linux 的 Windows 子系統

使用下列命令，在 Linux 或[適用於 Linux 的 Windows 子系統][install-wsl]上的 Bash 型殼層中，安裝 Istio `istioctl` 用戶端二進位檔。 這些命令會將 `istioctl` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

現在移至下一節[安裝在 AKS 上的 Istio Crd](#install-the-istio-crds-on-aks)。

### <a name="windows"></a>Windows

若要安裝 Istio`istioctl`用戶端在二進位**Powershell**-根據的 shell Windows，請使用下列命令。 這些命令複製`istioctl`用戶端二進位 Istio 資料夾並將它透過永久提供您`PATH`。 您不需要提高權限 （系統管理員） 權限，才能執行這些命令。

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

現在移至下一節[安裝在 AKS 上的 Istio Crd](#install-the-istio-crds-on-aks)。

## <a name="install-the-istio-crds-on-aks"></a>在 AKS 上安裝 Istio Crd

> [!IMPORTANT]
> 請確定在此區段中，執行步驟，從您下載並解壓縮的 Istio 版次的最上層資料夾。

使用 Istio[自訂資源定義 (Crd)] [ kubernetes-crd]來管理其執行階段組態。 我們需要 Istio Crd 先安裝，因為 Istio 元件在其上有相依性。 使用 Helm 和`istio-init`圖表，以安裝至 Istio Crd `istio-system` AKS 叢集中的命名空間：

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[作業][ kubernetes-jobs]部署成一部分`istio-init`Helm 圖表安裝 Crd。 這些作業花費 1 到 2 分鐘，才能完成，取決於您的叢集環境。 您可以確認該作業已順利完成，如下所示：

```azurecli
kubectl get jobs -n istio-system
```

下列範例輸出顯示已成功完成的作業。

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

既然我們已經確認成功完成的作業，讓我們確認我們已正確安裝的 Istio Crd 的數目。 您可以確認所有 53 Istio Crd，已安裝的執行適當的命令，為您的環境。 此命令應該傳回數目`53`。

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

如果您有到目前為止，這表示您已成功安裝 Istio Crd。 現在移至下一節[安裝在 AKS 上的 Istio 元件](#install-the-istio-components-on-aks)。

## <a name="install-the-istio-components-on-aks"></a>在 AKS 上安裝 Istio 元件

> [!IMPORTANT]
> 請確定在此區段中，執行步驟，從您下載並解壓縮的 Istio 版次的最上層資料夾。

我們將安裝[Grafana] [ grafana]並[Kiali] [ kiali]我們 Istio 安裝的一部分。 Grafana 提供分析和監視儀表板，而且 Kiali 提供服務網格可檢視性儀表板。 在我們的設定，每個元件都需要認證必須提供作為[祕密][kubernetes-secrets]。

我們可以安裝 Istio 元件之前，我們必須 Kiali 和 Grafana 來建立祕密。 執行適當的命令，為您的環境，以建立這些祕密。

### <a name="add-grafana-secret"></a>新增 Grafana 祕密

取代`REPLACE_WITH_YOUR_SECURE_PASSWORD`權杖與您的密碼，然後執行下列命令：

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>新增 Kiali 祕密

取代`REPLACE_WITH_YOUR_SECURE_PASSWORD`權杖與您的密碼，然後執行下列命令：

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>安裝 Istio 元件

既然我們已成功建立的 Grafana 和 Kiali 祕密，在我們的 AKS 叢集中，就可以安裝 Istio 元件。 使用 Helm 和`istio`圖表，以安裝至 Istio 元件`istio-system`AKS 叢集中的命名空間。 為您的環境中使用適當的命令。

> [!NOTE]
> 我們我們的安裝過程中使用下列選項：
> - `global.controlPlaneSecurityEnabled=true` -啟用控制平面相互 TLS
> - `mixer.adapters.useAdapterCRDs=false` -移除 mixer 介面卡 Crd 上的監看式，因為它們會被取代，而這將可改善效能
> - `grafana.enabled=true` -啟用分析的 Grafana 部署和監視儀表板
> - `grafana.security.enabled=true` -啟用 Grafana 的驗證
> - `tracing.enabled=true` -啟用追蹤的 Jaeger 部署
> - `kiali.enabled=true` -啟用 Kiali 部署服務網格可檢視性儀表板

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Powershell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

`istio` Helm 圖表部署大量的物件。 您可以看到的輸出清單您`helm install`上述命令。 Istio 元件部署可能需要 4 到 5 分鐘的時間才能完成，取決於您的叢集環境。

> [!NOTE]
> 在排程所有 Istio pod 時，必須在 Linux 節點上執行。 如果您除了 Linux 節點集區中有 Windows Server 的節點集區，在叢集上，確認所有 Istio pod，已都排程在 Linux 節點上執行。

此時，您已部署 Istio 至您的 AKS 叢集。 若要確保我們有 Istio 成功部署，讓我們移至下一節[驗證 Istio 安裝](#validate-the-istio-installation)。

## <a name="validate-the-istio-installation"></a>驗證 Istio 安裝

先確認是否已建立預期的服務。 使用 [kubectl get svc][kubectl-get] 命令來檢視執行中的服務。 查詢`istio-system`命名空間，Istio 和附加元件已安裝元件的`istio`Helm 圖表：

```console
kubectl get svc --namespace istio-system --output wide
```

下列範例輸出會顯示現在應該正在執行的服務：

- `istio-*` 服務
- `jaeger-*``tracing`，和`zipkin`附加元件的追蹤服務
- `prometheus` 附加元件計量服務
- `grafana` 附加元件的分析和監視儀表板服務
- `kiali` 附加元件服務網格儀表板服務

如果 `istio-ingressgateway` 顯示外部 IP 是 `<pending>`，請等候幾分鐘，直到 Azure 網路指派了 IP 位址。

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

接著，確認是否已建立所需的 Pod。 使用[kubectl get pods] [ kubectl-get]命令，並再次查詢`istio-system`命名空間：

```console
kubectl get pods --namespace istio-system
```

下列範例輸出會顯示正在執行的 Pod：

- `istio-*` pod
- `prometheus-*`附加元件度量 pod
- `grafana-*`附加元件的分析和監視儀表板 pod
- `kiali`附加元件服務網格儀表板 pod

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

應該是兩個`istio-init-crd-*`pod 數與`Completed`狀態。 這些 pod 會負責執行先前步驟中建立 Crd 的作業。 所有的其他 pod 應該會顯示狀態為`Running`。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 Pod 回報發生問題，請使用 [kubectl describe pod][kubectl-describe] 命令檢閱其輸出和狀態。

## <a name="accessing-the-add-ons"></a>存取附加元件

上方的設定中安裝了 Istio 和許多附加元件以提供額外功能。 附加元件的使用者介面不會透過外部 IP 位址來公開。 若要存取附加元件使用者介面，請使用 [kubectl port-forward][kubectl-port-forward] 命令。 此命令會建立用戶端電腦和相關的 pod 之間的安全連線，在您的 AKS 叢集。

我們稍早在本文中，為其指定的認證，Grafana 和 Kiali 新增額外的安全性。

### <a name="grafana"></a>Grafana

會提供分析和監視儀表板的 Istio [Grafana][grafana]。 將本機連接埠`3000`用戶端電腦上要連接埠`3000`在 AKS 叢集中執行 Grafana pod:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

下列範例輸出會顯示要為 Grafana 設定的連接埠轉送：

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

您現在可以在用戶端電腦上的下列 URL 連線至 Grafana - [http://localhost:3000](http://localhost:3000)。 請記得使用時所建立的 Grafana 透過祕密提示的認證。

### <a name="prometheus"></a>Prometheus

所提供的 Istio 計量[Prometheus][prometheus]。 將本機連接埠`9090`用戶端電腦上要連接埠`9090`在 AKS 叢集中執行 Prometheus pod:

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

追蹤內 Istio 由提供[Jaeger][jaeger]。 將本機連接埠`16686`用戶端電腦上要連接埠`16686`在 AKS 叢集中執行 Jaeger pod:

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

服務網格可檢視性儀表板會由 [Kiali][kiali] 來提供。 將本機連接埠`20001`用戶端電腦上要連接埠`20001`在 AKS 叢集中執行 Kiali pod:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

下列範例輸出會顯示要為 Kiali 設定的連接埠轉送：

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

您現在可以在用戶端電腦上的下列 URL 連線至 Kiali 服務網格可檢視性儀表板 - [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/)。 請記得使用您建立的認證透過 Kiali 祕密稍早出現提示時。

## <a name="uninstall-istio-from-aks"></a>從 AKS 解除安裝 Istio

> [!WARNING]
> 從執行中的系統中刪除 Istio 可能會導致流量相關問題，您的服務之間。 請確定您已佈建您的系統仍正常運作而不需要 Istio 後再繼續。

### <a name="remove-istio-components-and-namespace"></a>移除 Istio 元件和命名空間

若要移除 Istio 從您的 AKS 叢集，請使用下列命令。 `helm delete`命令會移除`istio`並`istio-init`圖表，而`kubectl delete ns`命令會移除`istio-system`命名空間。

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>移除 Istio Crd

上述命令會刪除所有 Istio 元件和命名空間，但我們仍處於使用 Istio Crd。 若要刪除 Crd，您可以使用其中一個下列的方法。

方法 #1-此命令假設您從已下載並解壓縮您用來安裝使用 Istio 的 Istio 發行的最上層資料夾執行此步驟。

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

這些命令，如果您無法再存取已下載並解壓縮您用來安裝使用 Istio 的 Istio 的發行方法 #2-使用下列其中一個。 此命令需要較長的時間-預期會需要幾分鐘才能完成。

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>後續步驟

下列文件說明如何使用 Istio 提供推出 canary 版本的智慧型路由：

> [!div class="nextstepaction"]
> [AKS Istio 智慧型路由案例][istio-scenario-routing]

若要深入探索 Istio 的安裝和組態選項，請參閱下列官方 Istio 文章：

- [Istio - Helm 安裝指南][istio-install-helm]
- [Istio - Helm 安裝選項][istio-install-helm-options]

您也可以使用 [Istio Bookinfo 應用程式範例][istio-bookinfo-example]來跟隨其他案例。

若要了解如何監視使用 Application Insights 和 Istio AKS 應用程式，請參閱下列 Azure 監視器文件：
- [Kubernetes 零檢測應用程式監視裝載之應用程式][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md