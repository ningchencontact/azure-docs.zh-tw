---
title: 疑難排解 |Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器
manager: douge
ms.openlocfilehash: 61bc081ca3221c0d588b7b7a2d9482d2fc70c0d5
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038061"
---
# <a name="troubleshooting-guide"></a>疑難排解指南

本指南包含您在使用 Azure Dev Spaces 時可能會遇到的常見問題相關資訊。

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>錯誤「無法建立 Azure Dev Spaces 控制器」

當發生錯誤而無法建立控制器時，您會看到此錯誤。 如果這事是暫時性錯誤，刪除並重新建立控制器即可修正此問題。

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

1. 開啟 [工具 > 選項]，然後在 [專案和解決方案] 底下選擇 [建置並執行]。
2. 將 **MSBuild 專案建置輸出詳細資訊**的設定變更為 [詳細] 或 [診斷]。

    ![工具選項對話方塊的螢幕擷取畫面](media/common/VerbositySetting.PNG)
    
## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>與 Dev Spaces 服務相關聯的公用 URL 進行 DNS 名稱解析失敗

發生這種情況時，當您嘗試連線至與 Dev Spaces 服務相關聯的公用 URL 時，網頁瀏覽器中可能會出現「無法顯示頁面」或「無法存取此網站」錯誤。

### <a name="try"></a>請嘗試︰

您可以使用下列命令列出所有與 Dev Spaces 服務相關聯的 URL：

```cmd
azds list-uris
```

如果 URL 處於「擱置」狀態，表示 Dev Spaces 仍在等候 DNS 註冊完成。 此作業有時需要數分鐘才能完成。 Dev Spaces 也會為每個服務開啟一個 localhost 通道，您在等候 DNS 登錄完成時可以使用此通道。

如果 URL 處於「擱置」狀態超過 5 分鐘，即表示負責取得公用端點的 nginx 輸入控制器可能有問題。 您可以使用下列命令刪除執行 nginx 控制器的 Pod。 它將會自動重新建立。

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>錯誤：「遺失必要的工具和組態」

啟動 VS Code 時可能會發生下列錯誤：「[Azure Dev Spaces] 遺失建置 '[專案名稱]' 及對其進行偵錯的必要工具和組態」。
此錯誤訊息表示 azds.exe 不在 PATH 環境變數中，如同 VS Code 中所示。

### <a name="try"></a>請嘗試︰

在已正確設定 PATH 環境變數的前提下，請從命令提示字元啟動 VS Code。

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>錯誤：'azds' 未辨識為內部或外部命令、可執行程式或批次檔
 
如果未安裝或正確設定 azds.exe，您可能會看到此錯誤訊息。

### <a name="try"></a>請嘗試︰

1. 請檢查位置 %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI (預覽) 中是否有 azds.exe。 如果有的話，請將該位置新增至 PATH 環境變數中。
2. 如果未安裝 azds.exe，請執行下列命令：

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>錯誤「上游連線錯誤或是在標頭前中斷連線/重設」
您在嘗試存取服務時，可能會看到這個錯誤。 例如，當您在瀏覽器中移至服務的 URL 時。 

### <a name="reason"></a>原因 
容器連接埠無法使用。 這個問題發生的原因： 
* 容器仍處於建置和部署程序。 如果您執行 `azds up` 或啟動偵錯工具，然後在容器成功部署之前嘗試存取容器，就有可能發生這個問題。
* 您的 _Dockerfile_、Helm 圖表及開啟連接埠的任何伺服器代碼之間的連接埠組態不一致。

### <a name="try"></a>請嘗試︰
1. 如果容器處於建置/部署程序，您可以等待 2-3 秒，然後再次嘗試存取服務。 
1. 查看您的連接埠組態。 以下所有資產中的指定連接埠號碼都應該**相同**：
    * **Dockerfile：** 由 `EXPOSE` 指示所指定。
    * **[Helm 圖表](https://docs.helm.sh)：** 由服務的 `externalPort` 和 `internalPort` 值 (通常位於 `values.yml` 檔案) 所指定。
    * 在應用程式程式碼中開啟的任何連接埠，例如在 Node.js 中：`var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>找不到組態檔
您執行 `azds up` 並且收到下列錯誤︰`Config file not found: .../azds.yaml`

### <a name="reason"></a>原因
您必須從想要執行的程式碼根目錄執行 `azds up`，而且您必須初始化程式碼資料夾，才能執行 Azure Dev Spaces。

### <a name="try"></a>請嘗試︰
1. 將目前目錄變更為內含服務程式碼的根資料夾。 
1. 如果您在程式碼資料夾中沒有 _azds.yaml_ 檔案，請執行 `azds prep` 以產生 Docker、Kubernetes 及 Azure Dev Spaces 資產。

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>錯誤：「管道程式 'azds' 非預期地結束，代碼 126」。
啟動 VS Code 偵錯工具有時候可能會導致這個錯誤。 這是已知的問題。

### <a name="try"></a>請嘗試︰
1. 請關閉 VS Code 然後重新開啟。
2. 再點擊一次 F5。


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>偵錯錯誤「不支援設定的偵錯類型 'coreclr'」
執行 VS Code 偵錯工具會回報錯誤：`Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>原因
您沒有 VS Code 擴充功能可以讓 Azure Dev Spaces 在您的開發機器上安裝。

### <a name="try"></a>請嘗試︰
安裝[適用於 Azure Dev Spaces 的 VS Code 擴充功能](get-started-netcore.md)。

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
當您管理 Azure Dev Spaces，以及在您沒有「擁有者」或「參與者」存取權的 Azure 訂用帳戶中工作時，可能會看到下列錯誤。
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>原因
選取的 Azure 訂用帳戶尚未註冊 `Microsoft.DevSpaces` 命名空間。

### <a name="try"></a>請嘗試︰
擁有 Azure 訂用帳戶「擁有者」或「參與者」存取權的使用者，可以執行下列 Azure CLI 命令，手動註冊 `Microsoft.DevSpaces` 命名空間：

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces 似乎未使用我現有的 Dockerfile 來建置容器 

### <a name="reason"></a>原因
Azure Dev Spaces 可以設定為指向您專案中的特定 _Dockerfile_。 如果發生 Azure Dev Spaces 未使用您預期的 _Dockerfile_ 來建置容器的情形，您可能需要明確告訴 Azure Dev Spaces 該檔案的位置。 

### <a name="try"></a>請嘗試︰
在專案中開啟由 Azure Dev Spaces 產生的 _azds.yaml_ 檔案。 請使用 `configurations->develop->build->dockerfile` 指示詞，以指向您想要使用的 Dockerfile：

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
