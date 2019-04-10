---
title: 部署 Azure Resource Manager 範本-Azure Logic Apps 與 logic apps
description: 使用 Azure Resource Manager 範本部署邏輯應用程式
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: bbb10bf0174b6e06e28d171510345ed92b6642d9
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357042"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>部署邏輯應用程式使用 Azure Resource Manager 範本

您建立的 Azure Resource Manager 範本部署邏輯應用程式之後，您可以部署您的範本，以下列方式：

* [Azure 入口網站](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>透過 Azure 入口網站部署

若要自動部署到 Azure 的邏輯應用程式範本，您可以選擇下列**部署至 Azure**按鈕，將您登入 Azure 入口網站，並提示您輸入您的邏輯應用程式的相關資訊。 您可以接著對任何必要的變更的邏輯應用程式範本或參數。

[![Deploy 至 Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

比方說，系統提示您輸入這項資訊之後登入 Azure 入口網站：

* Azure 訂用帳戶名稱
* 您想要使用的資源群組
* 邏輯應用程式位置
* 邏輯應用程式的名稱
* 測試 URI
* 接受指定的條款和條件

如需詳細資訊，請參閱 <<c0> [ 使用 Azure Resource Manager 範本與 Azure 入口網站部署資源](../azure-resource-manager/resource-group-template-deploy-portal.md)。

## <a name="authorize-oauth-connections"></a>授權 OAuth 連接

部署之後，邏輯應用程式就能搭配有效參數端對端運作。 不過，您仍然必須授權 OAuth 連接，才能產生有效的存取權杖。 自動部署，您可以使用指令碼來同意每個 OAuth 連接，這類[GitHub LogicAppConnectionAuth 專案中的範例指令碼](https://github.com/logicappsio/LogicAppConnectionAuth)。 您也可以在 Logic Apps 設計工具中開啟邏輯應用程式，以授權 OAuth 連接，透過 Azure 入口網站或 Visual Studio 中。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

若要部署至特定*Azure 資源群組*，使用下列命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

若要部署到特定的 Azure 訂用帳戶，使用下列命令：

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

若要部署至特定*Azure 資源群組*，使用下列命令：

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

若要部署到特定的 Azure 訂用帳戶，使用下列命令：

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

如需詳細資訊，請參閱下列主題： 

* [使用 Resource Manager 範本與 Azure CLI 部署資源](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>部署 Azure 的 devops

若要部署邏輯應用程式範本，並管理環境，小組通常使用一種工具這類[Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines)中[Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)。 提供 azure 的管線[Azure 資源群組部署工作](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)，您可以加入至任何組建或發行管線。
進行部署，並產生發行管線的授權，您也需要 Azure Active Directory (AD)[服務主體](../active-directory/develop/app-objects-and-service-principals.md)。 深入了解[使用服務主體與 Azure 管線](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。 

使用 Azure 管線的一般高層級步驟如下：

1. 在 Azure 管線中，建立空的管線。

1. 選擇，例如您的邏輯應用程式範本與您以手動方式或在建置程序產生的範本參數檔案，將管線所需的資源。

1. 為您的代理程式作業，找出並新增**Azure 資源群組部署**工作。

   ![將 「 Azure 資源群組部署 」 工作](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. 設定與[服務主體](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)。 

1. 將參考加入您的邏輯應用程式範本和範本參數檔案。

1. 視需要針對任何其他環境、自動化測試或核准者，繼續在發行程序中建置步驟。

## <a name="get-support"></a>取得支援

如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視邏輯應用程式](../logic-apps/logic-apps-monitor-your-logic-apps.md)
