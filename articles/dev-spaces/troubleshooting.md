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
keywords: 'Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，Helm，服務網格，服務網格路由、 kubectl，k8s '
ms.openlocfilehash: eff7f88ec6cbf8064df42fa3b22d61bb44baa451
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339579"
---
# <a name="troubleshooting-guide"></a>疑難排解指南

本指南包含您在使用 Azure Dev Spaces 時可能會遇到的常見問題相關資訊。

如果使用 Azure 開發人員空間時，您會有任何問題，建立[Azure 開發人員空格 GitHub 存放庫中的問題](https://github.com/Azure/dev-spaces/issues)。

## <a name="enabling-detailed-logging"></a>啟用詳細記錄

為了更有效地進行問題的疑難排解，建立更詳細的記錄以供檢閱可能會有幫助。

針對 Visual Studio 延伸模組，請將 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 環境變數設定為 1。 請務必重新啟動 Visual Studio，讓環境變數生效。 啟用之後，詳細記錄會寫入至您的 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 目錄。

在命令執行期間，您可以在 CLI 中使用 `--verbose` 參數輸出更多資訊。 您也可以在 `%TEMP%\Azure Dev Spaces` 中瀏覽更多詳細的記錄。 在 Mac 上，可以從終端機視窗執行 `echo $TMPDIR` 來找出 TEMP 目錄。 在 Linux 電腦上，TEMP 目錄通常是 `/tmp`。

## <a name="debugging-services-with-multiple-instances"></a>針對具有多個執行個體的服務進行偵錯

目前 Azure Dev Spaces 在針對單一執行個體 (或 Pod) 進行偵錯時效果最佳。 azds.yaml 檔案包含一個 replicaCount 設定，此設定會指出 Kubernetes 將為服務執行的 Pod 數目。 如果您變更 replicaCount 以將應用程式設定成針對指定的服務執行多個 Pod，偵錯工具將會連結至第一個 Pod (依字母順序列出時)。 偵錯工具會在該原始 Pod 回收時連結至不同的 Pod，而可能導致非預期的行為。

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>錯誤「無法建立 Azure Dev Spaces 控制器」

當發生錯誤而無法建立控制器時，您會看到此錯誤。 如果錯誤是暫時性的，刪除並重新建立控制器即可加以修正。

### <a name="try"></a>請嘗試︰

若要刪除控制器，請使用 Azure Dev Spaces CLI。 您無法在 Visual Studio 或 Cloud Shell 中這麼做。 若要安裝 AZDS CLI，請先安裝 Azure CLI，然後執行此命令：

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

接著，執行此命令來刪除控制器：

```cmd
azds remove -g <resource group name> -n <cluster name>
```

重新建立控制器的程序可透過 CLI 或 Visual Studio 來完成。 如同第一次啟動一般，請遵循教學課程中的指示。


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

1. 請檢查位置 %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (預覽) 中是否有 azds.exe。 如果有的話，請將該位置新增至 PATH 環境變數中。
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

### <a name="reason"></a>原因 
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

### <a name="reason"></a>原因
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

### <a name="reason"></a>原因
您的開發機器上未安裝適用於 C# 的 VS Code 延伸模組。 C#的擴充功能包含偵錯.NET Core (CoreCLR) 支援。

### <a name="try"></a>請嘗試︰
安裝[適用於 C# 的 VS Code 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>偵錯錯誤「不支援設定的偵錯類型 'coreclr'」
執行 VS Code 偵錯工具會回報錯誤：`Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>原因
您沒有 VS Code 擴充功能可以讓 Azure Dev Spaces 在您的開發機器上安裝。

### <a name="try"></a>請嘗試︰
安裝[適用於 Azure Dev Spaces 的 VS Code 擴充功能](get-started-netcore.md)。

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>偵錯錯誤「無效的 'cwd' 值 '/src'。 系統找不到指定的檔案。」 或「launch: program '/src/[專案二進位檔案路徑]' 不存在」
執行 VS Code 偵錯工具回報 `Invalid 'cwd' value '/src'. The system cannot find the file specified.` 和/或 `launch: program '/src/[path to project executable]' does not exist` 錯誤

### <a name="reason"></a>原因
VS Code 延伸模組預設會使用 `src` 作為容器上專案的工作目錄。 如果您已將 `Dockerfile` 更新成指定不同的工作目錄，就可能看到此錯誤。

### <a name="try"></a>請嘗試︰
更新您專案資料夾之 `.vscode` 子目錄底下的 `launch.json` 檔案。 將 `configurations->cwd` 指示詞變更成指向與您專案之 `Dockerfile`.中所定義 `WORKDIR` 相同的目錄。 您可能也需要更新 `configurations->program` 指示詞。

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>找不到類型或命名空間名稱 'MyLibrary'

### <a name="reason"></a>原因 
此建置內容預設是在專案/服務層級，因此找不到您所使用的程式庫專案。

### <a name="try"></a>請嘗試︰
必須執行什麼動作：
1. 修改 _azds.yaml_ 檔案以將建置內容設定到解決方案層級。
2. 修改 _Dockerfile_ 和 _Dockerfile.develop_ 檔案，以相對於新建置內容的方式，正確參考專案 (_.csproj_) 檔案。
3. 將 _.dockerignore_ 檔案放在 .sln 檔案旁邊，並且視需要加以修改。

您可以在 https://github.com/sgreenmsft/buildcontextsample 中找到範例

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' 授權錯誤
您需要 Azure 訂用帳戶中的「擁有者」或「參與者」權限才能管理 Azure Dev Spaces。 如果您嘗試管理 Dev Spaces，卻沒有相關聯 Azure 訂用帳戶的「擁有者」或「參與者」權限，就可能會看到此錯誤。
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>原因
選取的 Azure 訂用帳戶尚未註冊 `Microsoft.DevSpaces` 命名空間。

### <a name="try"></a>請嘗試︰
擁有 Azure 訂用帳戶「擁有者」或「參與者」存取權的使用者，可以執行下列 Azure CLI 命令，手動註冊 `Microsoft.DevSpaces` 命名空間：

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>AKS 虛擬節點在執行到「正在等待容器映像組建...」步驟時，Dev Spaces 發生逾時

### <a name="reason"></a>原因
當您嘗試執行設定上執行的 service fabric 中使用開發人員空間時，就會發生此逾時[AKS 虛擬節點](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)。 Dev Spaces 目前不支援在虛擬節點上建置服務或對服務進行偵錯。

如果您執行 `azds up` 並搭配 `--verbose` 參數，或在 Visual Studio 中啟用詳細資訊記錄，則會看到其他詳細資料：

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上述命令會顯示服務的 pod 已指派給*虛擬節點-aci-linux*，這是虛擬節點。

### <a name="try"></a>請嘗試︰
更新服務的 Helm 圖表，以移除任何允許服務在虛擬節點上執行的 nodeSelector 及/或 tolerations 值。 這些值通常會定義在圖表的 `values.yaml` 檔案中。

如果您想要透過 Dev Spaces 建置/偵錯的服務是在 VM 節點上執行的，則仍可使用已啟用虛擬節點功能的 AKS 叢集。 這是預設組態。

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>啟動 Dev Spaces 時出現「錯誤：找不到就緒的 Tiller Pod」

### <a name="reason"></a>原因
如果 Helm 用戶端無法再與叢集中執行的 Tiller Pod 通訊，就會發生此錯誤。

### <a name="try"></a>請嘗試︰
在您的叢集中重新啟動代理程式節點通常會解決這個問題。

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev Spaces Proxy 可能會干擾在開發空間中執行的其他 Pod

### <a name="reason"></a>原因
當您在 AKS 叢集中的命名空間上啟用 Dev Spaces 時，即會在每個於該命名空間內執行的 Pod 中額外安裝名為 _mindaro proxy_ 的容器。 此容器會攔截對 Pod 中之服務的呼叫，這對 Dev Spaces 小組開發功能而言是不可或缺的；不過，其可能會干擾在這些 Pod 中執行的特定服務。 其已知會干擾執行「Azure Redis 快取」的 Pod，導致在主要/從屬通訊中發生連線錯誤和失敗。

### <a name="try"></a>請嘗試︰
您可以將受影響的 Pod 移至未啟用 Dev Spaces 的叢集內所含的命名空間。 應用程式的其餘部分則可繼續在已啟用 Dev Spaces 的命名空間內執行。 Dev Spaces 將不會在啟用非 Dev Spaces 的命名空間內安裝 _mindaro-proxy_ 容器。

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces 似乎未使用我現有的 Dockerfile 來建置容器

### <a name="reason"></a>原因
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
