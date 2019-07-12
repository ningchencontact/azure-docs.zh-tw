---
title: 建立 Azure Logic Apps 的部署範本 | Microsoft Docs
description: 建立用來部署邏輯應用程式的 Azure Resource Manager 範本
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128595"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>建立用來部署邏輯應用程式的 Azure Resource Manager 範本

當您建立邏輯應用程式時，您可以展開您的邏輯應用程式定義[Azure Resource Manager 範本](../azure-resource-manager/resource-group-overview.md)。 您可以使用此範本藉由定義資源自動化部署和您想要用於部署，並提供參數值，透過參數[參數檔案](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)。
如此一來，您可以部署邏輯應用程式更容易，並在您想要的任何環境或 Azure 資源群組。 

Azure Logic Apps 提供[預先建置的邏輯應用程式的 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)，您可以重複使用，不只是用來建立邏輯應用程式，但若要定義的資源和部署使用的參數。 您可以在自己的商務案例中使用此範本，或自訂此範本以符合您的需求。 深入了解[Resource Manager 範本結構和語法](../azure-resource-manager/resource-group-authoring-templates.md)。 如需了解 JSON 語法和屬性，請參閱 [Microsoft.Logic 資源類型](/azure/templates/microsoft.logic/allversions)。

如需有關 Azure Resource Manager 範本的詳細資訊，請參閱下列文章：

* [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)
* [針對雲端一致性開發 Azure Resource Manager 範本](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>邏輯應用程式結構

邏輯應用程式定義具有下列基本章節，您可以藉由從 [設計工具檢視] 切換檢視，[程式碼檢視] 或藉由使用一種工具，例如[Azure 資源總管](http://resources.azure.com)。 邏輯應用程式定義使用 Javascript Object Notation (JSON)，因此如需有關 JSON 語法和屬性的詳細資訊，請參閱[Microsoft.Logic 資源類型](/azure/templates/microsoft.logic/allversions)。

* **邏輯應用程式資源**：描述定價方案，以及工作流程定義邏輯應用程式的位置或區域等資訊。

* **工作流程定義**：描述邏輯應用程式的觸發程序和動作，以及 Azure Logic Apps 服務的工作流程的執行方式。 在程式碼 檢視中，您可以找到工作流程定義`definition`一節。

* **連線**：如果您使用邏輯應用程式中的受管理的連接器`$connections`區段參考安全地儲存您的邏輯應用程式與其他系統或服務，例如連接字串和存取權杖之間的這些連線的相關中繼資料的其他資源。 在您的邏輯應用程式定義，這些連線的參考出現在邏輯應用程式定義的`parameters`一節。

若要建立邏輯應用程式範本，您可以搭配 Azure 資源群組部署，您必須定義的資源，並視需要參數化。 例如，如果要部署到開發、測試和生產環境，您可能想要在每個環境中使用不同連接字串連至 SQL Database。
或者，您可能想要在不同訂用帳戶或資源群組內部署。

## <a name="create-logic-app-deployment-templates"></a>建立邏輯應用程式部署範本

若要建立有效的邏輯應用程式部署範本的最簡單方式，使用 Visual Studio 和 Azure Logic Apps Tools for Visual Studio 擴充功能。 從 Azure 入口網站的邏輯應用程式下載到 Visual Studio 中，您會取得有效的部署範本，您可以使用任何 Azure 訂用帳戶和位置。 此外，下載您的邏輯應用程式會自動將內嵌在範本中的邏輯應用程式定義參數化。
如需有關建立和管理 Visual Studio 中的邏輯應用程式的詳細資訊，請參閱 <<c0> [ 使用 Visual Studio 中建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)並[管理邏輯應用程式，使用 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)。

非 Visual Studio 或以手動方式遵循本主題中的指引，以建立您的範本和必要的參數，您也可以使用[適用於建立邏輯應用程式範本 PowerShell 模組](https://github.com/jeffhollan/LogicAppTemplateCreator)。 這個開放原始碼模組會先評估邏輯應用程式與邏輯應用程式使用的任何連線。 此模組接著會產生所需的參數進行部署的範本資源。 例如，假設您有收到來自 Azure 服務匯流排佇列的訊息，並將資料加入至 Azure SQL database 的邏輯應用程式。 [模組] 工具會保留所有的協調流程邏輯，並會將參數化的 SQL 和服務匯流排連接字串，以便您可以在部署中設定這些值。

> [!IMPORTANT]
> 連線必須存在於相同 Azure 資源群組為邏輯應用程式。
> 若要使用任何 Azure 租用戶和訂用帳戶的存取權杖，使用的模組的 PowerShell 模組[Azure Resource Manager 用戶端工具](https://github.com/projectkudu/ARMClient)。 如需詳細資訊，請參閱此[Azure Resource Manager 用戶端工具的相關文件](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)更詳細地討論 ARMClient。

### <a name="install-powershell-module-for-logic-app-templates"></a>安裝適用於邏輯應用程式範本的 PowerShell 模組

針對要安裝的模組，從最簡單的方式[PowerShell 資源庫](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)，使用下列命令：

`Install-Module -Name LogicAppTemplate`

您也可以手動安裝 PowerShell 模組：

1. 下載最新[邏輯應用程式範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)。

1. 將解壓縮的資料夾，在 PowerShell 模組資料夾中，通常是`%UserProfile%\Documents\WindowsPowerShell\Modules`。

### <a name="generate-logic-app-template---powershell"></a>產生邏輯應用程式範本-PowerShell

安裝 PowerShell 之後，您可以使用下列命令來產生範本：

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 是 Azure 訂用帳戶識別碼。 這行程式碼會先透過 ARMClient 取得存取權杖，然後透過管線將它傳送到 PowerShell 指令碼，接著在 JSON 檔案中建立範本。

## <a name="parameters-in-logic-app-templates"></a>在 邏輯應用程式範本的參數

建立您的邏輯應用程式範本之後，您可以新增和編輯任何必要的參數。 您的範本有一個以上`parameters`區段，例如： 

* 邏輯應用程式的工作流程定義都有它自己[`parameters`一節](../logic-apps/logic-apps-workflow-definition-language.md#parameters)您可在其中定義邏輯應用程式會接受輸入，在部署時使用的所有參數。

* Resource Manager 範本都有它自己 [`parameters` 區段](../azure-resource-manager/resource-group-authoring-templates.md#parameters) 從邏輯應用程式的個別`parameters`一節。 例如:

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

例如，假設您的邏輯應用程式定義參考，代表 Azure 函式或巢狀的邏輯應用程式工作流程中，資源識別碼和您想要部署的資源識別碼，以及為單一部署邏輯應用程式。 您可以將該識別碼，為您的範本中的資源並參數化該識別碼。 您可以使用這個相同的方法的自訂 Api 或 OpenAPI 端點參考 (先前稱為"Swagger")，您想要部署與每個 Azure 資源群組。

當您在部署範本中使用參數時，請遵循本指導方針，讓 Logic Apps 設計工具可以正確顯示這些參數：

* 只有在這些觸發程序和動作中使用參數：

  * Azure Functions 應用程式
  * 巢狀或子邏輯應用程式工作流程
  * API 管理呼叫
  * API 連線執行階段 URL
  * API 連線路徑

* 當您定義參數時，請確定您提供預設值，使用`defaultValue`屬性值，例如：

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* 若要隱藏在範本中的機密資訊或保護，保護您的參數。 深入了解[如何使用安全的參數](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)。

以下是範例示範如何參數化的 Azure 服務匯流排 「 傳送訊息 」 動作：

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>參考相依的資源

如果您的邏輯應用程式需要相依資源的參考，您可以使用[Azure Resource Manager 範本函式](../azure-resource-manager/resource-group-template-functions.md)邏輯應用程式的部署範本中。 例如，假設您想要邏輯應用程式來參考 Azure 函式或整合帳戶與合作夥伴、 協議，和您想要與邏輯應用程式一起部署的其他成品的定義。
您可以使用 Resource Manager 範本函式，例如`parameters`， `variables`， `resourceId`， `concat`，依此類推。

以下是範例顯示如何藉由定義這些參數來取代 Azure 函式的資源識別碼：

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

以下是使用這些參數，參考 Azure 函式時：

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>將邏輯應用程式新增至資源群組專案

如果您有現有的 Azure 資源群組專案時，可以透過 [JSON 大綱] 視窗將邏輯應用程式新增至該專案。 您也可以您先前建立的應用程式共同新增另一個邏輯應用程式。

1. 在方案總管中開啟 `<template>.json` 檔案。

2. 從**檢視**功能表上，選取**其他 Windows** > **JSON 大綱**。

3. 將資源新增至範本檔案中，選擇**加入資源**頂端的 [JSON 大綱] 視窗。 或在 [JSON 大綱] 視窗中，以滑鼠右鍵按一下 [資源]  ，然後選取 [新增資源]  。

   ![[JSON 大綱] 視窗](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. 在 [新增資源]  對話方塊中，尋找並選取 [邏輯應用程式]  。 為您的邏輯應用程式命名，然後選擇 [新增]  。

   ![新增資源](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>取得支援

如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [部署邏輯應用程式範本](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
