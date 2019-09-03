---
title: 使用 Visual Studio Code 來建立及管理自動化工作流程 - Azure Logic Apps
description: 如何在 Visual Studio Code (VS Code) 中使用 JSON 來建立及管理邏輯應用程式的快速入門
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: f0b568289a50b9883e6e0f1cba6b793c9e6e90e8
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051739"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>快速入門：建立及管理自動化邏輯應用程式工作流程 - Visual Studio Code

使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 與 Visual Studio Code，您可以建立及管理有助於自動化工作、工作流程與程序的邏輯應用程式，以便整合各組織與企業的應用程式、資料、系統與服務。 此快速入門示範如何透過以程式碼為基礎的體驗，使用 JavaScript Object Notation (JSON) 來與工作流程定義結構描述搭配運作，藉以建立及編輯邏輯應用程式工作流程定義。 您也可以使用已部署至 Azure 的現有邏輯應用程式。 

雖然您可以在 Azure 入口網站(https://portal.azure.com) 與 Visual Studio 中執行這些相同的工作，但若您已熟悉邏輯應用程式定義，而想要直接在程式碼中工作時，使用 Visual Studio Code 將可更快展開工作。 例如，您可以停用、啟用、刪除及重新整理已建立的邏輯應用程式。 此外，您也可以從 Visual Studio Code 執行所在的任何開發平台 (例如 Linux、Windows、和 Mac) 來處理邏輯應用程式和整合帳戶。

在此文章中，您可以如[在 Azure 入口網站中建立邏輯應用程式的快速入門](../logic-apps/quickstart-create-first-logic-app-workflow.md)中所述，建立相同的邏輯應用程式，該文章比較著重在說明基本概念。 在 Visual Studio Code 中，邏輯應用程式看起來會像以下範例：

![完成的邏輯應用程式](./media/create-logic-apps-visual-studio-code/overview.png)

開始之前，請確定您有下列項目：

* 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [邏輯應用程式工作流程定義](../logic-apps/logic-apps-workflow-definition-language.md)與其結構 (使用 JavaScript 物件標記法 (JSON)) 的基本知識 

  如果您不熟悉 Logic Apps，請嘗試將逐步引導您[如何在 Azure 入口網站中建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的快速入門，其更專注於基本概念。 

* 可存取網路以登入 Azure 與您的 Azure 訂用帳戶

* 如果您還沒有以下這些工具，請加以下載並安裝： 

  * [Visual Studio Code 1.25.1 版或更新版本]("https://code.visualstudio.com/) (免費)

  * 適用於 Azure Logic Apps 的 Visual Studio Code 延伸模組

    您可以從 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) 或直接從 Visual Studio Code 內下載並安裝此擴充功能。 
    請務必在安裝之後重新載入 Visual Studio Code。 

    ![尋找「適用於 Azure Logic Apps 的 Visual Studio Code 擴充功能」](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    您可以透過 Visual Studio Code 工具列的 Azure 圖示，來確認延伸模組已正確安裝。 

    ![已安裝延伸模組](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    如需詳細資訊，請參閱[延伸模組市集](https://code.visualstudio.com/docs/editor/extension-gallery) \(英文\)。 您也可以瀏覽 [GitHub 上 Visual Studio Code 的 Azure Logic Apps 擴充功能](https://github.com/Microsoft/vscode-azurelogicapps)，以檢視並提交對此擴充功能的開放原始碼版本的貢獻。 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>登入 Azure

1. 開啟 Visual Studio Code。 在 Visual Studio Code 工具列上，選取 Azure 圖示。 

   ![選取 Azure 圖示](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. 在 Azure 視窗的 [Logic Apps]  下，選取 [登入 Azure]  。 

   ![選取 [登入 Azure]](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   系統現在會提示您使用提供的驗證碼登入。 

1. 複製驗證碼，然後選擇 [複製並開啟]  ，這會開啟新的瀏覽器視窗。

   ![登入提示](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. 輸入您的驗證碼。 出現提示時，請選擇 [繼續]  。

   ![輸入代碼](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. 選取您的 Azure 帳戶。 登入之後，您可以關閉瀏覽器，然後返回 Visual Studio Code。

   在 Azure 視窗中，[Logic Apps] 窗格與 [整合帳戶] 窗格現在會顯示您帳戶中的 Azure 訂用帳戶。 

   ![選取訂用帳戶](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   如果您沒看到預期的訂用帳戶，請選取 [Logic Apps]  標籤旁的 [選取訂用帳戶]  (過濾器圖示)。 尋找並選取您要的訂用帳戶。

1. 若要檢視 Azure 訂用帳戶中的任何現有邏輯應用程式或整合帳戶，請展開您的訂用帳戶。

   ![檢視邏輯應用程式與整合帳戶](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>建立邏輯應用程式

1. 如果您尚未從 Visual Studio Code 內登入 Azure 訂用帳戶，請依照此文章中的步驟[立即登入](#sign-in-azure)。

1. 從您訂用帳戶的內容功能表中，選取 [建立]  。

   ![選取 [建立]](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. 從顯示訂用帳戶的 Azure 資源群組清單中，選取現有資源群組或 [建立新的資源群組]  。 

   此範例會建立新的資源群組：

   ![建立新的資源群組](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. 提供您 Azure 資源群組的名稱，然後按 ENTER。

   ![為您的資源群組命名](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. 選取要儲存邏輯應用程式中繼資料的資料中心位置。

   ![選取位置](./media/create-logic-apps-visual-studio-code/select-location.png)

1. 為您的邏輯應用程式提供名稱，然後按 ENTER。

   ![為您的邏輯應用程式命名](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   新的邏輯應用程式現在會出現在 Azure 視窗中的 Azure 訂用帳戶下。 現在您可以開始建立邏輯應用程式的工作流程定義。

1. 從邏輯應用程式的捷徑功能表中，選取 [在編輯器中開啟]  。 

   ![在編輯器中開啟邏輯應用程式](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code 會開啟邏輯應用程式工作流程定義範本 (.logicapp.json 檔案)，讓您可以開始建立邏輯應用程式的工作流程。

   ![新的邏輯應用程式工作流程定義](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. 在邏輯應用程式工作流程定義範本檔案中，開始建置邏輯應用程式的工作流程定義。 如需技術參考，請參閱 [Azure Logic Apps 的工作流程定義語言結構描述](../logic-apps/logic-apps-workflow-definition-language.md)。

   以下是範例邏輯定義。 JSON 元素通常會依字母順序排列顯示在每個區段內，但此範例會大致以邏輯應用程式步驟出現在設計工具上的順序顯示這些元素。

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. 當您完成後，請儲存邏輯應用程式定義檔案。 當 Visual Studio Code 提示您確認將邏輯應用程式定義上傳至 Azure 訂用帳戶時，請選擇 [上傳]  。

   ![上傳新的邏輯應用程式](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   在 Visual Studio Code 將邏輯應用程式發佈至 Azure 之後，您就可以在 Azure 入口網站中發現您的應用程式已上線並執行中。 

   ![在 Azure 入口網站中發佈的邏輯應用程式](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>編輯邏輯應用程式

若要使用已部署在 Azure 中的現有邏輯應用程式，您可以在 Visual Studio Code 中開啟該應用程式的工作流程定義檔案。

1. 如果您尚未從 Visual Studio Code 內登入 Azure 訂用帳戶，請依照此文章中的步驟[立即登入](#sign-in-azure)。

1. 在 Azure 視窗的 [Logic Apps]  下，展開 Azure 訂用帳戶，並選取您要的邏輯應用程式。 

1. 從邏輯應用程式的功能表中，選取 [在編輯器中開啟]  。 或者，在邏輯應用程式的名稱旁，選擇編輯圖示。

   ![針對現有邏輯應用程式開啟編輯器](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code 會開啟 .logicapp.json 檔案，以供邏輯應用程式的工作流程定義使用。

   ![開啟的邏輯應用程式工作流程定義](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. 在邏輯應用程式的定義中進行變更。

1. 完成後，儲存變更。

1. 當 Visual Studio Code 提示您更新 Azure 訂用帳戶中的邏輯應用程式定義時，請選擇 [上傳]  。 

   ![上傳您的編輯](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> * [使用 Visual Studio 建立邏輯應用程式](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)