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
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199291"
---
# <a name="troubleshooting-guide"></a>疑難排解指南

本指南包含您在使用 Azure Dev Spaces 時可能會遇到的常見問題相關資訊。

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>錯誤「上游連線錯誤或是在標頭前中斷連線/重設」
您在嘗試存取服務時，可能會看到這個錯誤。 例如，當您在瀏覽器中移至服務的 URL 時。 

### <a name="reason"></a>原因 
容器連接埠無法使用。 可能原因是： 
* 容器仍處於建置和部署程序。 如果您執行 `azds up` 或啟動偵錯工具，然後在容器成功部署之前嘗試存取容器，就有可能發生這種情形。
* 您的 Dockerfile、Helm 圖表及開啟連接埠的任何伺服器代碼之間的連接埠組態不一致。

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
1. 如果您在程式碼資料夾中沒有 azds.yaml 檔案，請執行 `azds prep` 以產生 Docker、Kubernetes 及 Azure Dev Spaces 資產。

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
1. 修改 azds.yaml 檔案以將建置內容設定到解決方案層級。
2. 修改 Dockerfile 和 Dockerfile.develop 檔案，以相對於新建置內容的方式，正確參考 csproj 檔案。
3. 將 .dockerignore 檔案放在 .sln 檔案旁邊，並且視需要加以修改。

您可以在 https://github.com/sgreenmsft/buildcontextsample 中找到範例

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>'Microsoft.ConnectedEnvironment/register/action' 授權錯誤
當您管理 Azure Dev Spaces，以及在您沒有「擁有者」或「參與者」存取權的 Azure 訂用帳戶中工作時，可能會看到下列錯誤。
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>原因
選取的 Azure 訂用帳戶尚未註冊 Microsoft.ConnectedEnvironment 命名空間。

### <a name="try"></a>請嘗試︰
擁有 Azure 訂用帳戶「擁有者」或「參與者」存取權的某位使用者，可以執行下列 Azure CLI 命令，手動註冊 Microsoft.ConnectedEnvironment 命名空間：

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces 似乎未使用我現有的 Dockerfile 來建置容器 

### <a name="reason"></a>原因
Azure Dev Spaces 可以設定為指向您專案中的特定 Dockerfile。 如果發生 Azure Dev Spaces 未使用您預期的 Dockerfile 來建置容器的情形，您可能需要明確告訴 Azure Dev Spaces 該檔案的位置。 

### <a name="try"></a>請嘗試︰
在專案中開啟由 Azure Dev Spaces 產生的 `azds.yaml` 檔案。 請使用 `configurations->develop->build->dockerfile` 指示詞，以指向您想要使用的 Dockerfile：

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```