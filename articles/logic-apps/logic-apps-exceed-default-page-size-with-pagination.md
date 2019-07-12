---
title: 取得更多的資料、 項目或使用分頁-Azure Logic Apps 的記錄
description: 設定分頁超過 Azure Logic Apps 中的連接器動作的預設頁面大小限制
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64476537"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Azure Logic Apps 中使用分頁來取得更多的資料、 項目或記錄

當使用中的連接器動作來擷取資料、 項目或資料錄[Azure Logic Apps](../logic-apps/logic-apps-overview.md)，您可能會收到結果集太大的動作不會傳回所有結果，在相同的時間。 執行某些動作的結果數目可能會超過連接器的預設頁面大小。 在此情況下，動作只會傳回結果的第一頁。 例如，SQL Server 連接器的預設頁面大小**取得資料列**動作為 2048，但可能會改變根據其他設定。

有些動作可讓您開啟*分頁*設定，如此您邏輯應用程式可以擷取更多的結果，最大分頁限制，但動作完成時，這些結果傳回為單一訊息。 當您使用的分頁時，您必須指定*閾值*值，也就是您想要傳回的動作結果的目標數目。 動作會擷取結果，直到到達指定的臨界值。 當您的項目總數小於指定的臨界值時，則動作會擷取所有結果。

開啟連接器的頁面大小為基礎的結果分頁設定擷取頁面。 這個行為表示，某些情況下，您可能會收到結果超過您指定的臨界值。 例如，當使用 SQL Server**取得資料列**支援分頁設定的動作：

* 動作的預設頁面大小為 2048年每頁記錄數。
* 假設您有 10,000 筆記錄，並指定最小值的 5000 筆記錄。
* 分頁取得頁面的記錄，因此至少可以取得指定的最小值，此動作會傳回 6144 記錄 （3 個頁面 x 2048 記錄），不 5000 筆的記錄。

以下是一些您可以在其中超過特定動作的預設頁面大小的連接器清單：

* [Azure Blob 儲存體](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 邏輯應用程式，您要開啟重新編頁的動作。 如果您沒有在邏輯應用程式，請參閱[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="turn-on-pagination"></a>開啟分頁

若要判斷邏輯應用程式設計工具中支援分頁的動作，檢查動作的設定**分頁**設定。 此範例示範如何開啟 SQL Server 中的分頁**取得資料列**動作。

1. 在動作的右上角，選擇省略符號 ( **...** ) 按鈕，然後選取**設定**。

   ![開啟動作的設定](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   如果動作支援分頁，動作就會顯示**分頁**設定。

1. 變更**分頁**從設定**Off**來**上**。 在 **閾值**屬性，指定您想要傳回的動作結果的目標數目的整數值。

   ![指定要傳回結果的最小數目](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 準備就緒時，請選擇 [完成]  。

## <a name="workflow-definition---pagination"></a>工作流程定義-分頁

當您開啟分頁支援此功能的動作時，邏輯應用程式的工作流程定義會包含`"paginationPolicy"`屬性連同`"minimumItemCount"`屬性，該動作中的`"runtimeConfiguration"`內容，例如：

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>取得支援

如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
