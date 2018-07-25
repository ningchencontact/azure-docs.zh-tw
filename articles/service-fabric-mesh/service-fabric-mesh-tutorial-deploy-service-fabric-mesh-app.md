---
title: 教學課程 - 將 Service Fabric Mesh 應用程式部署至 Service Fabric Mesh | Microsoft Docs
description: 了解如何發佈有 ASP.NET Core 網站可與後端 Web 服務進行通訊的 Azure Service Fabric Mesh 應用程式。
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: f9dea759f6556bc521dda4efbd27176f1e06452b
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126570"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>教學課程：部署 Service Fabric Mesh Web 應用程式

本教學課程是系列中的第三部分，說明如何直接從 Visual Studio 發佈 Azure Service Fabric Mesh Web 應用程式。

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 將應用程式發佈至 Azure。
> * 檢查應用程式部署狀態
> * 查看目前已部署至您的訂用帳戶的所有應用程式
> * 檢視應用程式記錄
> * 清除應用程式所使用的資源。

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [建置 Service Fabric Mesh Web 應用程式](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [在本機對應用程式偵錯](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * 將應用程式發佈至 Azure

您將了解如何建立具有 ASP.NET Web 前端和 ASP.NET Core Web API 後端服務的 Azure Service Fabric Mesh 應用程式。 接著，您將在本機開發叢集上偵錯應用程式，並將應用程式發佈至 Azure。 當您完成作業時，您將會有簡單的待辦事項應用程式，示範如何在 Service Fabric Mesh Web 應用程式中發出服務對服務呼叫。

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前：

* 如果您沒有 Azure 訂用帳戶，您可以在開始前[建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 確定您已[設定開發環境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)，包括安裝 Service Fabric 執行階段、SDK、Docker 和 Visual Studio 2017。

## <a name="download-the-to-do-sample-application"></a>下載待辦事項範例應用程式

如果您未在[本教學課程系列的第二部分](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)中建置待辦事項範例應用程式，您可以在此時下載。 在命令視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦。

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

該應用程式位於 `src\todolistapp` 目錄下。

## <a name="publish-to-azure"></a>發佈至 Azure

若要將您的 Service Fabric Mesh 專案發佈至 Azure，請在 Visual Studio 中以滑鼠右鍵按一下 **ServiceFabricMeshApp**，然後選取 [發佈...]。

接著，您將會看見 [發佈 Service Fabric 應用程式] 對話方塊。

![Visual Studio 的 Service Fabric Mesh 發佈對話方塊](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

選取您的 Azure 帳戶和訂用帳戶。 選擇 [位置] 。 本文使用**美國東部**。

在 [資源群組] 下方，選取 [\<建立新的資源群組...>]。 對話方塊隨即出現，而您將在其中建立新的資源群組。 本文使用**美國東部**的位置，並將群組命名為 **sfmeshTutorial1RG** (如果您的組織有多位人員使用同一個訂用帳戶，請選擇唯一的群組名稱)。  按 [建立] 以建立資源群組並返回發佈對話方塊。

![Visual Studio 的 Service Fabric Mesh 新增資源群組對話方塊](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

返回 [發佈 Service Fabric 應用程式] 對話方塊，在 [Azure Container Registry] 下方，選取 [\<建立新的容器登錄...>]。 在 [建立容器登錄] 對話方塊中，針對**容器登錄名稱**使用唯一的名稱。 指定**位置** (本教學課程使用**美國東部**)。 從下拉式清單中選取您在上一個步驟中建立的**資源群組**，例如 **sfmeshTutorial1RG**。 將 **SKU** 設定為 [基本]，然後按 [建立] 以返回發佈對話方塊。

![Visual Studio 的 Service Fabric Mesh 新增資源群組對話方塊](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

如果發生錯誤，表示資源提供者尚未在您的訂用帳戶上註冊，您可加以註冊。 請先查看資源提供者是否可供您的訂用帳戶使用：

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

如果容器登錄提供者 (`Microsoft.ContainerRegistry`) 可供使用，請從 Powershell 加以註冊：

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

在發佈對話方塊中按 [發佈] 按鈕，將您的 Service Fabric 應用程式部署至 Azure。

當您首次發佈到 Azure 時，會將 Dcker 映像推送到 Azure Container Registry (ACR)，所需時間取決於映像大小。 後續發佈相同專案的速度將會加快。 您可以在 Visual Studio 的 [輸出] 視窗中選取 [Service Fabric 工具]，藉以監視部署的進度。 在部署完成後，**Service Fabric 工具**輸出將會以 URL 形式顯示應用程式的 IP 位址和連接埠。

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

開啟網頁瀏覽器，並瀏覽至 URL 以檢視在 Azure 中執行的網站。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成其餘步驟。

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.35 版或更新版本。 執行 `az --version` 以尋找版本。 若要安裝或升級至最新版本的 CLI，請參閱[安裝 Azure CLI 2.0][azure-cli-install]。

## <a name="install-the-az-mesh-cli"></a>安裝 az mesh cli
在 CLI 提示字元上

1) 請移除任何先前安裝的 Azure Service Fabric Mesh CLI 模組。

```cli
az extension remove --name mesh
```

2)  安裝 Azure Service Fabric Mesh CLI 擴充功能模組。 在預覽版本中，Azure Service Fabric Mesh CLI 是以 Azure CLI 擴充功能的形式撰寫，但在公開預覽版則會隨 Azure CLI 提供。

```cli
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="check-application-deployment-status"></a>檢查應用程式部署狀態

此時，您的應用程式已完成部署。 您可以使用 `app show` 命令來檢查其狀態。 

教學課程應用程式的應用程式名稱是 `ServiceMeshApp`。 請使用下列命令來收集應用程式的詳細資料：

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>查看目前已部署至您的訂用帳戶的所有應用程式

您可以使用「應用程式清單」命令，取得您已部署至訂用帳戶的應用程式清單。

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>檢視應用程式記錄

查看已部署應用程式的記錄：

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>清除資源

不再需要時，請刪除您所建立的所有資源。 由於您已建立新的資源群組來裝載 ACR 和 Service Fabric Mesh 服務資源，您可以安全地刪除此資源群組，而這也會刪除所有相關聯的資源。

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

或者，您可以[從入口網站](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources)刪除資源群組。 

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解：
> [!div class="checklist"]
> * 將應用程式發佈至 Azure。
> * 檢查應用程式部署狀態
> * 查看目前已部署至您的訂用帳戶的所有應用程式
> * 檢視應用程式記錄
> * 清除應用程式所使用的資源。

現在您已將 Service Fabric Mesh 應用程式發佈至 Azure，接著請嘗試下列作業：

* 瀏覽[投票應用程式範例](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)，以檢視服務對服務通訊的另一個範例。
* 閱讀 [Service Fabric 資源](service-fabric-mesh-service-fabric-resources.md)
* 閱讀 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 相關資訊

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest