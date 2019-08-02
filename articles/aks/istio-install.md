---
title: 在 Azure Kubernetes Service (AKS) 中安裝 Istio
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中安裝和使用 Istio 以建立服務網格
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9d973cb2ac210e912d93941a2f81889557379f43
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "67625984"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中安裝和使用 Istio

[Istio][istio-github]是一種開放原始碼服務網格, 可在 Kubernetes 叢集中跨微服務提供一組重要的功能。 這些功能包括流量管理、服務識別和安全性、原則強制執行和可檢視性。 如需有關 Istio 的詳細資訊, 請參閱官方[什麼是 Istio？][istio-docs-concepts]檔。

本文說明如何安裝 Istio。 Istio `istioctl`用戶端二進位檔會安裝到您的用戶端電腦上, 而 Istio 元件會安裝到 AKS 上的 Kubernetes 叢集。

> [!NOTE]
> 這些指示參考 Istio 版本`1.1.3`。
>
> Istio 小組`1.1.x`已針對 Kubernetes 版本`1.11`( `1.12`、) `1.13`測試 Istio 版本。 您可以在[GitHub Istio 版本][istio-github-releases]中找到其他 Istio 版本, 並在[Istio 版本][istio-release-notes]資訊中找到每個版本的相關資訊。

在本文中，您將了解：

> [!div class="checklist"]
> * 下載 Istio
> * 安裝 Istio istioctl 用戶端二進位檔
> * 在 AKS 上安裝 Istio CRDs
> * 在 AKS 上安裝 Istio 元件
> * 驗證 Istio 安裝
> * 存取附加元件
> * 從 AKS 卸載 Istio

## <a name="before-you-begin"></a>開始之前

本文中詳述的步驟假設您已`1.11` `kubectl`建立 AKS 叢集 (已啟用 RBAC 的 Kubernetes 和更新版本), 並已建立與叢集的連線。 如果您需要這些專案的協助, 請參閱[AKS 快速入門][aks-quickstart]。

您將需要[Helm][helm] , 才能遵循這些指示並安裝 Istio。 建議您在叢集中正確安裝`2.12.2`和設定版本或更新版本。 如果您需要安裝 Helm 的協助, 請參閱[AKS Helm 安裝指引][helm-install]。 所有 Istio pod 也必須排程在 Linux 節點上執行。

本文將 Istio 安裝指引分成數個獨立的步驟。 最終結果在結構上與官方 Istio 安裝[指導][istio-install-helm]方針相同。

## <a name="download-istio"></a>下載 Istio

首先，下載並解壓縮最新的 Istio 版本。 在 MacOS、Linux 或適用于 Linux 的 Windows 子系統上的 bash shell 和 PowerShell shell 的步驟稍有不同。 選擇下列其中一個符合您慣用環境的安裝步驟:

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

現在, 請移至區段以[安裝 Istio istioctl 用戶端二進位](#install-the-istio-istioctl-client-binary)檔。

### <a name="powershell"></a>PowerShell

在 PowerShell 中, `Invoke-WebRequest`使用下載最新的 Istio 版本, 然後以`Expand-Archive`解壓縮, 如下所示:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

現在, 請移至區段以[安裝 Istio istioctl 用戶端二進位](#install-the-istio-istioctl-client-binary)檔。

## <a name="install-the-istio-istioctl-client-binary"></a>安裝 Istio istioctl 用戶端二進位檔

> [!IMPORTANT]
> 請確定您是從已下載並解壓縮之 Istio 版本的最上層資料夾, 執行本節中的步驟。

`istioctl`用戶端二進位檔會在您的用戶端電腦上執行, 並可讓您與 Istio 服務網格互動。 在用戶端作業系統之間, 安裝步驟稍有不同。 選擇下列其中一個符合您慣用環境的安裝步驟:

* [MacOS](#macos)
* [Linux 或適用于 Linux 的 Windows 子系統](#linux-or-windows-subsystem-for-linux)
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

現在請繼續進行下一節, 以[在 AKS 上安裝 Istio CRDs](#install-the-istio-crds-on-aks)。

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux 或適用於 Linux 的 Windows 子系統

使用下列命令, 在 linux 或`istioctl` [適用于 linux 的 Windows 子系統][install-wsl]上以 bash 為基礎的 shell 中安裝 Istio 用戶端二進位檔。 這些命令會將 `istioctl` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

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

現在請繼續進行下一節, 以[在 AKS 上安裝 Istio CRDs](#install-the-istio-crds-on-aks)。

### <a name="windows"></a>Windows

若要在 Windows `istioctl`上以**Powershell**為基礎的 shell 中安裝 Istio 用戶端二進位檔, 請使用下列命令。 這些命令會將`istioctl`用戶端二進位檔複製到 Istio 資料夾, 然後透過您`PATH`的, 以立即方式 (在目前的 shell 中) 和永久 (跨 shell 重新開機) 來使用它。 您不需要更高的 (系統管理員) 許可權來執行這些命令, 也不需要重新開機 shell。

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

現在請繼續進行下一節, 以[在 AKS 上安裝 Istio CRDs](#install-the-istio-crds-on-aks)。

## <a name="install-the-istio-crds-on-aks"></a>在 AKS 上安裝 Istio CRDs

> [!IMPORTANT]
> 請確定您是從已下載並解壓縮之 Istio 版本的最上層資料夾, 執行本節中的步驟。

Istio 會使用[自訂資源定義 (CRDs)][kubernetes-crd]來管理其執行時間設定。 我們必須先安裝 Istio CRDs, 因為 Istio 元件有相依性。 使用 Helm 和`istio-init`圖表將 Istio CRDs 安裝到 AKS 叢集中`istio-system`的命名空間:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[作業][kubernetes-jobs]會部署為`istio-init` Helm 圖表的一部分, 以安裝 CRDs。 根據您的叢集環境而定, 這些作業應需要1到2分鐘的時間才能完成。 您可以驗證作業是否已順利完成, 如下所示:

```azurecli
kubectl get jobs -n istio-system
```

下列範例輸出顯示已成功完成的作業。

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

既然我們已確認成功完成作業, 讓我們來確認是否已安裝正確的 Istio CRDs 數目。 您可以針對您的環境執行適當的命令, 以確認已安裝所有 53 Istio CRDs。 此命令應該會傳回數位`53`。

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

如果您已經開始, 這表示您已成功安裝 Istio CRDs。 現在請移至下一節, 以[在 AKS 上安裝 Istio 元件](#install-the-istio-components-on-aks)。

## <a name="install-the-istio-components-on-aks"></a>在 AKS 上安裝 Istio 元件

> [!IMPORTANT]
> 請確定您是從已下載並解壓縮之 Istio 版本的最上層資料夾, 執行本節中的步驟。

我們將在 Istio 安裝過程中安裝[Grafana][grafana]和[Kiali][kiali] 。 Grafana 提供分析和監視儀表板, 而 Kiali 則提供服務網格可檢視性儀表板。 在我們的設定中, 每個元件都需要認證, 必須以[秘密][kubernetes-secrets]的形式提供。

在我們可以安裝 Istio 元件之前, 必須先為 Grafana 和 Kiali 建立秘密。 針對您的環境執行適當的命令, 以建立這些秘密。

### <a name="add-grafana-secret"></a>新增 Grafana 秘密

以您`REPLACE_WITH_YOUR_SECURE_PASSWORD`的密碼取代權杖, 然後執行下列命令:

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

### <a name="add-kiali-secret"></a>新增 Kiali 秘密

以您`REPLACE_WITH_YOUR_SECURE_PASSWORD`的密碼取代權杖, 然後執行下列命令:

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

既然我們已成功建立 AKS 叢集中的 Grafana 和 Kiali 秘密, 就可以安裝 Istio 元件了。 使用 Helm 和`istio`圖表將 Istio 元件安裝到 AKS 叢集中`istio-system`的命名空間。 針對您的環境使用適當的命令。

> [!NOTE]
> 我們會在安裝過程中使用下列選項:
> - `global.controlPlaneSecurityEnabled=true`-已啟用控制平面的相互 TLS
> - `mixer.adapters.useAdapterCRDs=false`-移除混音器介面卡 CRDs 上的監看, 使其被淘汰, 這將可改善效能
> - `grafana.enabled=true`-啟用分析和監視儀表板的 Grafana 部署
> - `grafana.security.enabled=true`-啟用 Grafana 的驗證
> - `tracing.enabled=true`-啟用 Jaeger 部署以進行追蹤
> - `kiali.enabled=true`-啟用服務網格可檢視性儀表板的 Kiali 部署

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

`istio` Helm 圖表會部署大量物件。 您可以從上述`helm install`命令的輸出查看清單。 Istio 元件的部署可能需要4到5分鐘的時間才能完成, 視您的叢集環境而定。

> [!NOTE]
> 所有 Istio pod 都必須排程在 Linux 節點上執行。 如果您的叢集上有 Linux 節點集區以外的 Windows Server 節點集區, 請確認所有 Istio pod 已排程在 Linux 節點上執行。

此時, 您已將 Istio 部署至 AKS 叢集。 為了確保我們已成功部署 Istio, 讓我們繼續進行下一節, 以[驗證 Istio 安裝](#validate-the-istio-installation)。

## <a name="validate-the-istio-installation"></a>驗證 Istio 安裝

先確認是否已建立預期的服務。 使用[kubectl get svc][kubectl-get]命令來查看執行中的服務。 查詢命名空間, 其中 Istio 和附加元件是`istio`由 Helm 圖表所安裝: `istio-system`

```console
kubectl get svc --namespace istio-system --output wide
```

下列範例輸出會顯示現在應該正在執行的服務：

- `istio-*`伺服器
- `jaeger-*`、 `tracing`和`zipkin`附加元件追蹤服務
- `prometheus`附加元件計量服務
- `grafana`附加元件分析和監視儀表板服務
- `kiali`附加元件服務網格儀表板服務

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

接著，確認是否已建立所需的 Pod。 使用[kubectl get][kubectl-get] pod 命令, 然後重新查詢`istio-system`命名空間:

```console
kubectl get pods --namespace istio-system
```

下列範例輸出會顯示正在執行的 Pod：

- `istio-*` pod
- `prometheus-*`附加元件計量 pod
- `grafana-*`附加元件分析和監視儀表板 pod
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

應該有兩個`istio-init-crd-*` `Completed`具有狀態的 pod。 這些 pod 負責執行在先前步驟中建立 CRDs 的工作。 所有其他 pod 都應該顯示狀態`Running`。 如果您的 Pod 沒有這些狀態，請等候一兩分鐘直到其成為該狀態。 如果有任何 pod 回報問題, 請使用[kubectl 描述 pod][kubectl-describe]命令來檢查其輸出和狀態。

## <a name="accessing-the-add-ons"></a>存取附加元件

上方的設定中安裝了 Istio 和許多附加元件以提供額外功能。 附加元件的使用者介面不會透過外部 IP 位址來公開。 若要存取附加元件使用者介面, 請使用[kubectl 埠轉送][kubectl-port-forward]命令。 此命令會在您的用戶端電腦與 AKS 叢集中的相關 pod 之間建立安全連線。

我們為 Grafana 和 Kiali 新增了一層額外的安全性, 方法是在本文稍早的指定認證。

### <a name="grafana"></a>Grafana

Istio 的分析和監視儀表板是由[Grafana][grafana]提供。 將用戶端電腦`3000`上的本機埠轉送至`3000` AKS 叢集中執行 Grafana 的 pod 上的埠:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

下列範例輸出會顯示要為 Grafana 設定的連接埠轉送：

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

您現在可以在用戶端電腦上的下列 URL 連線至 Grafana - [http://localhost:3000](http://localhost:3000)。 請記得在出現提示時, 使用您稍早透過 Grafana 密碼所建立的認證。

### <a name="prometheus"></a>Prometheus

[Prometheus][prometheus]會提供 Istio 的計量。 將用戶端電腦`9090`上的本機埠轉送至`9090` AKS 叢集中執行 Prometheus 的 pod 上的埠:

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

Istio 內的追蹤是由[Jaeger][jaeger]提供。 將用戶端電腦`16686`上的本機埠轉送至`16686` AKS 叢集中執行 Jaeger 的 pod 上的埠:

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

服務網格可檢視性儀表板是由[Kiali][kiali]提供。 將用戶端電腦`20001`上的本機埠轉送至`20001` AKS 叢集中執行 Kiali 的 pod 上的埠:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

下列範例輸出會顯示要為 Kiali 設定的連接埠轉送：

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

您現在可以在用戶端電腦上的下列 URL 連線至 Kiali 服務網格可檢視性儀表板 - [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/)。 請記得在出現提示時, 使用您稍早透過 Kiali 密碼所建立的認證。

## <a name="uninstall-istio-from-aks"></a>從 AKS 卸載 Istio

> [!WARNING]
> 從執行中的系統刪除 Istio 可能會導致您的服務之間發生流量相關的問題。 請確定您已在不 Istio 的情況下, 將您的系統布建為正常運作, 再繼續進行。

### <a name="remove-istio-components-and-namespace"></a>移除 Istio 元件和命名空間

若要從您的 AKS 叢集中移除 Istio, 請使用下列命令。 這些`helm delete`命令將會`istio`移除`istio-init` 和`istio-system`圖表, 而命令會移除命名空間。`kubectl delete ns`

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>移除 Istio CRDs

上述命令會刪除所有 Istio 元件和命名空間, 但我們仍留在 Istio CRDs。 若要刪除 CRDs, 您可以使用下列其中一種方法。

#1 的方法-此命令假設您是從您用來安裝 Istio 之 Istio 的已下載和解壓縮版本的最上層資料夾執行此步驟。

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

方法 #2-如果您無法再存取用來安裝 Istio 之 Istio 的已下載和解壓縮版本, 請使用下列其中一個命令。 此命令將需要較長的時間-預期需要幾分鐘的時間才能完成。

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>後續步驟

下列檔說明如何使用 Istio 來提供智慧型路由, 以推出未排放的版本:

> [!div class="nextstepaction"]
> [AKS Istio 智慧型路由案例][istio-scenario-routing]

若要深入探索 Istio 的安裝和組態選項，請參閱下列官方 Istio 文章：

- [Istio-Helm 安裝指南][istio-install-helm]
- [Istio-Helm 安裝選項][istio-install-helm-options]

您也可以使用[Istio Bookinfo 應用程式範例][istio-bookinfo-example], 遵循其他案例。

若要瞭解如何使用 Application Insights 和 Istio 監視您的 AKS 應用程式, 請參閱下列 Azure 監視器檔:
- [Kubernetes 託管應用程式的零檢測應用程式監視][app-insights]

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
