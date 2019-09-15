---
title: 疑難排解
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s '
ms.openlocfilehash: b16a7d874f15747c14df1d728be824fac76de2be
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993953"
---
# <a name="troubleshooting-guide"></a>疑難排解指南

本指南包含您在使用 Azure Dev Spaces 時可能會遇到的常見問題相關資訊。

如果您在使用 Azure Dev Spaces 時遇到問題, 請[在 Azure Dev Spaces GitHub 存放庫中建立問題](https://github.com/Azure/dev-spaces/issues)。

## <a name="enabling-detailed-logging"></a>啟用詳細記錄

為了更有效地進行問題的疑難排解，建立更詳細的記錄以供檢閱可能會有幫助。

針對 Visual Studio 延伸模組，請將 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 環境變數設定為 1。 請務必重新啟動 Visual Studio，讓環境變數生效。 啟用之後，詳細記錄會寫入至您的 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目錄。

在命令執行期間，您可以在 CLI 中使用 `--verbose` 參數輸出更多資訊。 您也可以在 `%TEMP%\Azure Dev Spaces` 中瀏覽更多詳細的記錄。 在 Mac 上，可以從終端機視窗執行 `echo $TMPDIR` 來找出 TEMP 目錄。 在 Linux 電腦上，TEMP 目錄通常是 `/tmp`。

## <a name="debugging-services-with-multiple-instances"></a>針對具有多個執行個體的服務進行偵錯

目前 Azure Dev Spaces 在針對單一執行個體 (或 Pod) 進行偵錯時效果最佳。 azds.yaml 檔案包含一個 replicaCount 設定，此設定會指出 Kubernetes 將為服務執行的 Pod 數目。 如果您變更 replicaCount 以將應用程式設定成針對指定的服務執行多個 Pod，偵錯工具將會連結至第一個 Pod (依字母順序列出時)。 偵錯工具會在該原始 Pod 回收時連結至不同的 Pod，而可能導致非預期的行為。

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>錯誤「無法建立 Azure Dev Spaces 控制器」

### <a name="reason"></a>`Reason`
當發生錯誤而無法建立控制器時，您會看到此錯誤。 如果錯誤是暫時性的，刪除並重新建立控制器即可加以修正。

### <a name="try"></a>嘗試

刪除控制器:

```bash
azds remove -g <resource group name> -n <cluster name>
```

您必須使用 Azure Dev Spaces CLI 來刪除控制器。 不可能從 Visual Studio 刪除控制器。 您也無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI, 因此無法從 Azure Cloud Shell 中刪除控制器。

如果您未安裝 Azure Dev Spaces CLI, 您可以先使用下列命令來安裝它, 然後刪除您的控制器:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

重新建立控制器的程序可透過 CLI 或 Visual Studio 來完成。 如需範例, 請參閱[使用 .Net Core](quickstart-netcore-visualstudio.md)快速入門進行[小組開發](quickstart-team-development.md)或開發。

## <a name="error-service-cannot-be-started"></a>錯誤：「服務無法啟動。」

當您的服務程式碼無法啟動時，您可能會看到此錯誤訊息。 通常是使用者程式碼所造成的。 若要取得更詳細的診斷資訊，請對您的命令與設定進行下列變更：

### <a name="try"></a>請嘗試︰

在命令列上：

使用 _azds.exe_ 時，請使用 --verbose 命令列選項並使用 --output 命令列選項，來指定輸出格式。
 
```cmd
azds up --verbose --output json
```

在 Visual Studio 中：

1. 開啟 [工具] > [選項]，然後在 [專案和解決方案] 底下選擇 [建置並執行]。
2. 將 **MSBuild 專案建置輸出詳細資訊**的設定變更為 [詳細] 或 [診斷]。

    ![工具選項對話方塊的螢幕擷取畫面](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>多階段 Dockerfile：
在使用多階段 Dockerfile 時，您會收到「服務無法啟動」的錯誤。 在此情況下，詳細資訊輸出會包含下列文字：

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

之所以會發生此錯誤，是因為 AKS 節點執行的是較舊版 Docker，不支援多階段組建。 若要避免多階段組建，請重寫 Dockerfile。

### <a name="rerunning-a-service-after-controller-re-creation"></a>在重新建立控制器之後重新執行服務
在將與此叢集關聯的 Azure Dev Spaces 控制器移除並再予以重新建立之後，嘗試重新執行服務時，您會收到「服務無法啟動」的錯誤。 在此情況下，詳細資訊輸出會包含下列文字：

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

之所以會發生此錯誤，是因為移除 Dev Spaces 控制器並不會移除該控制器先前所安裝的服務。 重新建立控制器後再嘗試使用新控制器來執行服務會失敗，因為舊服務仍在原位。

若要解決此問題，請使用 `kubectl delete` 命令來手動從叢集中移除舊服務，然後重新執行 Dev Spaces 以安裝新服務。

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>與 Dev Spaces 服務相關聯的公用 URL 進行 DNS 名稱解析失敗

您可以藉由對 `azds prep` 命令指定 `--public` 參數，或藉由在 Visual Studio 中選取 `Publicly Accessible` 核取方塊，來為服務設定公用 URL 端點。 當您在 Dev Spaces 中執行服務時，公用 DNS 名稱會自動完成註冊。 如果此 DNS 名稱未完成註冊，您就會在連線至公用 URL 時，於網頁瀏覽器中看到「頁面無法顯示」或「網站無法連線」的錯誤。

### <a name="try"></a>請嘗試︰

您可以使用下列命令列出所有與 Dev Spaces 服務相關聯的 URL：

```cmd
azds list-uris
```

如果 URL 處於「擱置」狀態，表示 Dev Spaces 仍在等候 DNS 註冊完成。 有時需要幾分鐘的時間才能完成註冊。 Dev Spaces 也會為每個服務開啟一個 localhost 通道，您在等候 DNS 登錄完成時可以使用此通道。

如果 URL 處於「擱置」狀態超過 5 分鐘，可能表示建立公用端點的外部 DNS Pod 或取得公用端點的 nginx 輸入控制器 Pod 有問題。 您可以使用下列命令來刪除這些 Pod。 AKS 會自動重新建立已刪除的 Pod。

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>錯誤：「遺失必要的工具和組態」

啟動 VS Code 時可能會發生下列錯誤：「[Azure Dev Spaces] 遺失建置 '[專案名稱]' 及對其進行偵錯的必要工具和組態」。
此錯誤訊息表示 azds.exe 不在 PATH 環境變數中，如同 VS Code 中所示。

### <a name="try"></a>請嘗試︰

在已正確設定 PATH 環境變數的前提下，請從命令提示字元啟動 VS Code。

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>錯誤「建置 'projectname' 並對其進行偵錯所需的工具已過時。」

如果您有適用於 Azure Dev Spaces 的新版 VS Code 延伸模組，但 Azure Dev Spaces CLI 為舊版，就會在 Visual Studio Code 中看到此錯誤。

### <a name="try"></a>嘗試

下載並安裝最新版的 Azure Dev Spaces CLI：

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>錯誤：'azds' 未辨識為內部或外部命令、可執行程式或批次檔
 
如果未安裝或正確設定 azds.exe，您可能會看到此錯誤訊息。

### <a name="try"></a>請嘗試︰

1. 檢查 azds 的位置% ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI。 如果有的話，請將該位置新增至 PATH 環境變數中。
2. 如果未安裝 azds.exe，請執行下列命令：

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>「因語言不受支援而無法產生 Dockerfile」的警告
Azure Dev Spaces 提供 C# 和 Node.js 的原生支援。 如果您在目錄中執行 *azds prep*，而該目錄包含以其中一種語言撰寫的程式碼，Azure Dev Spaces 將自動為您建立適當的 Dockerfile。

您仍可透過以其他語言撰寫的程式碼來使用 Azure Dev Spaces，但在第一次執行 azds up 之前，您必須先手動建立 Dockerfile。

### <a name="try"></a>請嘗試︰
如果您的應用程式是以 Azure Dev Spaces 未原生支援的語言所撰寫，您就必須提供適當的 Dockerfile，建置用來執行程式碼的容器映像。 Docker 提供[撰寫 Dockerfile 的最佳做法清單](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)及 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/)，可協助您撰寫符合需求的 Dockerfile。

在您備妥適當的 Dockerfile 後，您即可繼續執行 *azds up*，以在 Azure Dev Spaces 中執行您的應用程式。

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>錯誤「上游連線錯誤或是在標頭前中斷連線/重設」
您在嘗試存取服務時，可能會看到這個錯誤。 例如，當您在瀏覽器中移至服務的 URL 時。 

### <a name="reason"></a>`Reason` 
容器連接埠無法使用。 這個問題發生的原因： 
* 容器仍處於建置和部署程序。 如果您執行 `azds up` 或啟動偵錯工具，然後在容器成功部署之前嘗試存取容器，就有可能發生這個問題。
* 您的 _Dockerfile_、Helm 圖表及開啟連接埠的任何伺服器代碼之間的連接埠組態不一致。

### <a name="try"></a>請嘗試︰
1. 如果容器處於建置/部署程序，您可以等待 2-3 秒，然後再次嘗試存取服務。 
1. 查看您的連接埠組態。 下列所有資產中的指定連接埠號碼都應該**相同**：
    * **Dockerfile：** 由 `EXPOSE` 指令所指定。
    * **[Helm 圖表](https://docs.helm.sh)：** 由服務的 `externalPort` 和 `internalPort` 值 (通常位於 `values.yml` 檔案) 所指定。
    * 在應用程式程式碼中開啟的任何連接埠，例如在 Node.js 中：`var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>找不到組態檔
您執行 `azds up` 並且收到下列錯誤︰`Config file not found: .../azds.yaml`

### <a name="reason"></a>`Reason`
您必須從想要執行的程式碼根目錄執行 `azds up`，而且您必須初始化程式碼資料夾，才能執行 Azure Dev Spaces。

### <a name="try"></a>請嘗試︰
1. 將目前目錄變更為內含服務程式碼的根資料夾。 
1. 如果您在程式碼資料夾中沒有 _azds.yaml_ 檔案，請執行 `azds prep` 以產生 Docker、Kubernetes 及 Azure Dev Spaces 資產。

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Error:「管道程式 'azds' 意外結束，代碼為 126。」
啟動 VS Code 偵錯工具有時候可能會導致這個錯誤。

### <a name="try"></a>請嘗試︰
1. 請關閉 VS Code 然後重新開啟。
2. 再點擊一次 F5。

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>偵錯錯誤「找不到 coreclr 類型的偵測工具擴充」
執行 VS Code 偵錯工具會回報錯誤：`Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>`Reason`
您的開發機器上未安裝適用於 C# 的 VS Code 延伸模組。 此C#延伸模組包含 .net Core (CoreCLR) 的偵錯工具支援。

### <a name="try"></a>請嘗試︰
安裝[適用於 C# 的 VS Code 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>偵錯錯誤「不支援設定的偵錯類型 'coreclr'」
執行 VS Code 偵錯工具會回報錯誤：`Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>`Reason`
您沒有 VS Code 擴充功能可以讓 Azure Dev Spaces 在您的開發機器上安裝。

### <a name="try"></a>請嘗試︰
安裝[適用於 Azure Dev Spaces 的 VS Code 擴充功能](get-started-netcore.md)。

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>偵錯錯誤「無效的 'cwd' 值 '/src'。 系統找不到指定的檔案。」 或「launch: program '/src/[專案二進位檔案路徑]' 不存在」
執行 VS Code 偵錯工具回報 `Invalid 'cwd' value '/src'. The system cannot find the file specified.` 和/或 `launch: program '/src/[path to project executable]' does not exist` 錯誤

### <a name="reason"></a>`Reason`
VS Code 延伸模組預設會使用 `src` 作為容器上專案的工作目錄。 如果您已將 `Dockerfile` 更新成指定不同的工作目錄，就可能看到此錯誤。

### <a name="try"></a>請嘗試︰
更新您專案資料夾之 `.vscode` 子目錄底下的 `launch.json` 檔案。 將 `configurations->cwd` 指示詞變更成指向與您專案之 `Dockerfile`.中所定義 `WORKDIR` 相同的目錄。 您可能也需要更新 `configurations->program` 指示詞。

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>找不到類型或命名空間名稱 'MyLibrary'

### <a name="reason"></a>`Reason` 
此建置內容預設是在專案/服務層級，因此找不到您所使用的程式庫專案。

### <a name="try"></a>請嘗試︰
必須執行什麼動作：
1. 修改 _azds.yaml_ 檔案以將建置內容設定到解決方案層級。
2. 修改 _Dockerfile_ 和 _Dockerfile.develop_ 檔案，以相對於新建置內容的方式，正確參考專案 ( _.csproj_) 檔案。
3. 將 _.dockerignore_ 檔案放在 .sln 檔案旁邊，並且視需要加以修改。

您可以在 https://github.com/sgreenmsft/buildcontextsample 中找到範例

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' 授權錯誤
您需要 Azure 訂用帳戶中的「擁有者」或「參與者」權限才能管理 Azure Dev Spaces。 如果您嘗試管理 Dev Spaces，卻沒有相關聯 Azure 訂用帳戶的「擁有者」或「參與者」權限，就可能會看到此錯誤。
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>`Reason`
選取的 Azure 訂用帳戶尚未註冊 `Microsoft.DevSpaces` 命名空間。

### <a name="try"></a>請嘗試︰
擁有 Azure 訂用帳戶「擁有者」或「參與者」存取權的使用者，可以執行下列 Azure CLI 命令，手動註冊 `Microsoft.DevSpaces` 命名空間：

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>AKS 虛擬節點在執行到「正在等待容器映像組建...」步驟時，Dev Spaces 發生逾時

### <a name="reason"></a>`Reason`
當您嘗試使用 Dev Spaces 來執行設定為在[AKS 虛擬節點](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)上執行的服務時, 就會發生此超時。 Dev Spaces 目前不支援在虛擬節點上建置服務或對服務進行偵錯。

如果您執行 `azds up` 並搭配 `--verbose` 參數，或在 Visual Studio 中啟用詳細資訊記錄，則會看到其他詳細資料：

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上述命令顯示服務的 pod 已指派給*虛擬節點-aci-linux*, 也就是虛擬節點。

### <a name="try"></a>請嘗試︰
更新服務的 Helm 圖表，以移除任何允許服務在虛擬節點上執行的 nodeSelector 及/或 tolerations 值。 這些值通常會定義在圖表的 `values.yaml` 檔案中。

如果您想要透過 Dev Spaces 建置/偵錯的服務是在 VM 節點上執行的，則仍可使用已啟用虛擬節點功能的 AKS 叢集。 這是預設組態。

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>啟動 Dev Spaces 時出現「錯誤：找不到就緒的 Tiller Pod」

### <a name="reason"></a>`Reason`
如果 Helm 用戶端無法再與叢集中執行的 Tiller Pod 通訊，就會發生此錯誤。

### <a name="try"></a>請嘗試︰
在您的叢集中重新啟動代理程式節點通常會解決這個問題。

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>「錯誤: 發行 azds-\<識別碼\> - \<spacename\> servicename失敗\> : 服務的servicename\< - \< \>' 已存在」或「已拒絕 servicename \< \>的提取存取權, 存放庫不存在, 或可能需要 ' docker login ' "

### <a name="reason"></a>`Reason`
如果您在相同的開發人員空間內混合執行直接 Helm 命令`helm install`( `helm upgrade`例如、 `helm delete`或`azds up` ) 與 dev Spaces 命令 (例如和`azds down`), 就會發生這些錯誤。 因為 Dev Spaces 有自己的 Tiller 實例, 所以會與您在相同開發人員空間中執行的自有 Tiller 實例相衝突, 因此會發生這種情況。

### <a name="try"></a>請嘗試︰
您可以針對相同的 AKS 叢集使用 Helm 命令和 Dev Spaces 命令, 但是每個啟用 Dev Spaces 的命名空間都應該使用其中一個。

例如, 假設您使用 Helm 命令, 在父開發人員空間中執行整個應用程式。 您可以建立該父系的子開發人員空間, 使用 Dev Spaces 在子開發人員空間內執行個別服務, 並一起測試服務。 當您準備好簽入變更時, 請使用 Helm 命令, 將更新的程式碼部署至父開發人員空間。 請勿使用`azds up`在父開發人員空間中執行更新的服務, 因為它會與使用 Helm 一開始執行的服務發生衝突。

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev Spaces Proxy 可能會干擾在開發空間中執行的其他 Pod

### <a name="reason"></a>`Reason`
當您在 AKS 叢集中的命名空間上啟用 Dev Spaces 時，即會在每個於該命名空間內執行的 Pod 中額外安裝名為 _mindaro proxy_ 的容器。 此容器會攔截對 Pod 中之服務的呼叫，這對 Dev Spaces 小組開發功能而言是不可或缺的；不過，其可能會干擾在這些 Pod 中執行的特定服務。 已知會干擾執行 Azure Cache for Redis 的 pod, 導致主要/次要通訊中發生連線錯誤和失敗。

### <a name="try"></a>請嘗試︰
您可以將受影響的 Pod 移至未啟用 Dev Spaces 的叢集內所含的命名空間。 應用程式的其餘部分則可繼續在已啟用 Dev Spaces 的命名空間內執行。 Dev Spaces 將不會在啟用非 Dev Spaces 的命名空間內安裝 _mindaro-proxy_ 容器。

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces 似乎未使用我現有的 Dockerfile 來建置容器

### <a name="reason"></a>`Reason`
Azure Dev Spaces 可以設定為指向您專案中的特定 _Dockerfile_。 如果發生 Azure Dev Spaces 未使用您預期的 Dockerfile 來建置容器的情形，您可能需要明確告訴 Azure Dev Spaces 要使用哪個 Dockerfile。 

### <a name="try"></a>請嘗試︰
在專案中開啟 Azure Dev Spaces 所產生的 azds.yaml 檔案。 使用 *configurations->develop->build->dockerfile* 指示詞，以指向您想要使用的 Dockerfile：

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>將偵錯工具附加至 node.js 應用程式時發生錯誤「內部監看失敗: 監看 ENOSPC」

### <a name="reason"></a>`Reason`

執行 pod 的節點與您嘗試附加至偵錯工具的 node.js 應用程式已超過*inotifypropertychanged. max _user_watches*值。 在某些情況下， [ *inotifypropertychanged*的預設值可能太小，無法直接處理將偵錯工具附加至 pod](https://github.com/Azure/AKS/issues/772)。

### <a name="try"></a>嘗試
此問題的暫時因應措施是增加叢集中每個節點上的*inotifypropertychanged 最大 _user_watches*值，然後重新開機該節點，讓變更生效。

## <a name="new-pods-are-not-starting"></a>未啟動新的 pod

### <a name="reason"></a>`Reason`

Kubernetes 初始化運算式無法針對新的 pod 套用 PodSpec, 因為叢集中對叢集*管理員*角色的 RBAC 許可權變更。 新的 pod 可能也會有不正確 PodSpec, 例如與 pod 相關聯的服務帳戶已不存在。 若要查看因初始化運算式問題而處於*擱置*狀態的 pod, 請使用`kubectl get pods`命令:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

此問題可能會影響叢集中*所有命名空間*中的 pod, 包括未啟用 Azure Dev Spaces 的命名空間。

### <a name="try"></a>嘗試

將[Dev SPACES CLI 更新為最新版本](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools), 然後從 Azure Dev Spaces 控制器刪除*azds InitializerConfiguration* :

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

當您從 Azure Dev Spaces 控制器移除*azds InitializerConfiguration*之後, 請使用`kubectl delete`移除處於*擱置*狀態的任何 pod。 移除所有擱置中的 pod 之後, 請重新部署您的 pod。

如果新的 pod 在重新部署後仍處於*擱置*狀態, 請使用`kubectl delete`移除處於*擱置*狀態的任何 pod。 移除所有擱置中的 pod 之後, 請從叢集刪除控制器並重新安裝:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

重新安裝您的控制器之後, 請重新部署您的 pod。

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>呼叫 Dev Spaces 控制器和 Api 的 RBAC 許可權不正確

### <a name="reason"></a>`Reason`
存取 Azure Dev Spaces 控制器的使用者必須具有讀取 AKS 叢集上系統管理員*kubeconfig*的存取權。 例如, 您可以在內建的 Azure Kubernetes Service 叢集系統[管理員角色](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)中取得此許可權。 存取 Azure Dev Spaces 控制器的使用者也必須擁有該控制器的「*參與者*」或「*擁有*者」 RBAC 角色。

### <a name="try"></a>嘗試
如需更新使用者 AKS 叢集許可權的詳細資訊, 請參閱[這裡](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)。

若要更新該控制器的使用者 RBAC 角色:

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 流覽至包含控制器的資源群組, 這通常與您的 AKS 叢集相同。
1. 啟用 [*顯示隱藏的類型*] 核取方塊。
1. 按一下控制器。
1. 開啟 [*存取控制 (IAM)* ] 窗格。
1. 按一下 [*角色指派*] 索引標籤。
1. 依序按一下 [*新增*] 和 [*新增角色指派*]。
    * 針對 [*角色*], 選取 [*參與者*] 或 [*擁有*者]。
    * 針對 [*指派存取*權] *, 選取 Azure AD 使用者、群組或服務主體*。
    * 針對 [*選取*], 搜尋您想要授與許可權的使用者。
1. 按一下 [儲存]。

## <a name="controller-create-failing-due-to-controller-name-length"></a>控制器建立失敗, 因為控制器名稱長度

### <a name="reason"></a>`Reason`
Azure Dev Spaces 控制器的名稱不能超過31個字元。 當您在 AKS 叢集上啟用 Dev Spaces 或建立控制器時, 如果您的控制器名稱超過31個字元, 您會收到類似下列的錯誤:

*無法為叢集「a-控制器名稱---------aks-us」建立 Dev Spaces 控制器:Azure Dev Spaces 控制器名稱 ' a--aks-美國--us ' 不正確, 因此無法使用。違反的條件約束:Azure Dev Spaces 控制器名稱長度最多隻能有31個字元*

### <a name="try"></a>嘗試

建立具有替代名稱的控制器:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>將 Windows 節點集區新增至 AKS 叢集時啟用 Dev Spaces 失敗

### <a name="reason"></a>`Reason`
目前, Azure Dev Spaces 僅適用于在 Linux pod 和節點上執行。 當您的 AKS 叢集具有 Windows 節點集區時, 您必須確定只在 Linux 節點上排程 Azure Dev Spaces pod。 如果 Azure Dev Spaces pod 已排程在 Windows 節點上執行, 該 pod 將不會啟動, 且啟用 Dev Spaces 將會失敗。

### <a name="try"></a>嘗試
[將污點新增](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)至您的 AKS 叢集, 以確保 Linux pod 不會排程在 Windows 節點上執行。

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>「在叢集上找不到處于就緒狀態的 untainted Linux 節點」錯誤。 必須至少有一個 untainted Linux 節點處於 [就緒] 狀態, 才能在「azds」命名空間中部署 pod。」

### <a name="reason"></a>`Reason`

Azure Dev Spaces 無法在 AKS 叢集上建立控制器, 因為它找不到處于*就緒*狀態的 untainted 節點來排程 pod。 Azure Dev Spaces 需要至少一個處於 [*就緒*] 狀態的 Linux 節點, 以便在不指定容差的情況下排程 pod。

### <a name="try"></a>嘗試
在您的 AKS 叢集上[更新您的污點](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)設定, 以確保至少有一個 Linux 節點允許排程 pod, 而不指定容差。 此外, 請確定至少有一個 Linux 節點允許在不指定容差的情況下排程 pod, 處於*就緒*狀態。 如果您的節點花費較長的時間來達到 [*就緒*] 狀態, 您可以嘗試重新開機您的節點。

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>執行時發生「Azure Dev Spaces CLI 未正確安裝」錯誤`az aks use-dev-spaces`

### <a name="reason"></a>`Reason`
Azure Dev Spaces CLI 的更新已變更其安裝路徑。 如果您使用2.0.63 之前的 Azure CLI 版本, 您可能會看到此錯誤。 若要顯示您的 Azure CLI 版本, 請`az --version`使用。

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

儘管在2.0.63 之前`az aks use-dev-spaces`以 Azure CLI 版本執行時的錯誤訊息, 安裝還是會成功。 您可以繼續使用`azds` , 而不會發生任何問題。

### <a name="try"></a>嘗試
將[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)安裝更新為2.0.63 或更新版本。 這會解決您在執行時`az aks use-dev-spaces`收到的錯誤訊息。 或者, 您可以繼續使用目前版本的 Azure CLI 和 Azure Dev Spaces CLI。


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>水準 pod 自動調整無法在開發人員空間中運作

### <a name="reason"></a>`Reason`

當您在開發人員空間中執行服務時，該服務的 pod 會[插入其他容器以進行檢測](how-dev-spaces-works.md#prepare-your-aks-cluster)，而 pod 中的所有容器都必須針對水準 pod 自動調整設定資源限制和要求。 


將`azds.io/proxy-resources`注釋新增至您的 pod 規格，即可針對插入的容器（devspaces-proxy）套用資源要求和限制。值應該設定為 JSON 物件，代表 proxy 之容器規格的 resources 區段。

### <a name="try"></a>嘗試

以下是要套用至 pod 規格的 proxy 資源注釋範例。
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

## <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>嘗試從私人登錄使用 Docker 映射時發生「未經授權：需要驗證」錯誤

### <a name="reason"></a>`Reason`

您正在使用需要驗證的私人登錄中的 Docker 映射。 您可以使用[imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)，讓 Dev Spaces 進行驗證，並從這個私人登錄提取映射。

### <a name="try"></a>嘗試

若要使用 imagePullSecrets，請在您要使用映射的命名空間中[建立 Kubernetes 秘密](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)。 然後在中`azds.yaml`提供密碼作為 imagePullSecret。

以下是在中`azds.yaml`指定 imagePullSecrets 的範例。

```
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> 在中`azds.yaml`設定 imagePullSecrets 將會覆寫中`values.yaml`指定的 imagePullSecrets。
