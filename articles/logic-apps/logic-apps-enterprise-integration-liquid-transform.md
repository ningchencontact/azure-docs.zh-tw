---
title: 使用 Liquid 轉換來轉換 JSON 資料 - Azure Logic Apps | Microsoft Docs
description: 使用 Logic Apps 和 Liquid 範本建立進階 JSON 轉換的轉換或對應。
services: logic-apps
documentationcenter: ''
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 42a102c9b2663380a93d56cc5f8fb82abaa83b74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299506"
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>使用 Liquid 範本執行進階的 JSON 轉換

Azure Logic Apps 支援透過原生資料作業動作進行的基本 JSON 轉換，例如**撰寫**或**剖析 JSON**。 至於進階的 JSON 轉換，可以使用 Liquid 範本搭配您的邏輯應用程式。 
[Liquid](https://shopify.github.io/liquid/) 是一種開放原始碼的範本語言，適合靈活的 Web 應用程式使用。
 
透過本文，可瞭解如何使用 Liquid 對應或範本，其可支援更複雜的 JSON 轉換，例如反覆項目、控制流程、變數等等。 您在邏輯應用程式中執行 Liquid 轉換之前，必須先使用 Liquid 對應來定義從 JSON 到 JSON 的對應，並在您的企業整合帳戶中儲存該對應。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[開始使用免費 Azure 帳戶](https://azure.microsoft.com/free/)。 否則，您可以[註冊隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 基本[企業整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>為您的企業整合帳戶建立 Liquid 範本或對應

1. 建立此範例的 Liquid 範本。 Liquid 範本會定義如何轉換 JSON 輸入，如下所示：

   ``` json
   {%- assign deviceList = content.devices | Split: ', ' -%}
      {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
      }
   ```
   > [!NOTE]
   > 如果您的 Liquid 範本有使用任何[篩選條件](https://shopify.github.io/liquid/basics/introduction/#filters)，篩選條件都必須以大寫字母開頭。 

2. 登入 [Azure 入口網站](https://portal.azure.com)。

3. 在主要 Azure 功能表上，選擇 [所有資源]。 

4. 在搜尋方塊中，尋找並選取整合帳戶。

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  在企業整合帳戶圖格上，選取 [對應]。

   ![選取對應](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. 選擇 [新增]，並提供該對應的以下詳細資料：

   * **名稱**：對應的名稱，在此範例中是「JsontoJsonTemplate」
   * **對應類型**：對應的類型。 對於 JSON 到 JSON 的轉換，請選取 [Liquid]。
   * **對應**：用於轉換的現有 Liquid 範本或對應檔案，在此範例中是「SimpleJsonToJsonTemplate.liquid」。 若要尋找此檔案，您可以使用檔案選擇器。

   ![新增 Liquid 範本](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>新增 JSON 轉換的 Liquid 動作

1. [建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

2. 將[要求觸發程序](../connectors/connectors-native-reqres.md#use-the-http-request-trigger)新增至邏輯應用程式。

3. 選擇 [+ 新步驟] > [新增動作]。 在搜尋方塊中，輸入 [Liquid]，然後選取 [Liquid -將 JSON 轉換為 JSON]。

   ![尋找並選取 Liquid 動作](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. 在 [內容] 方塊中，選取動態內容清單或參數清單 (以出現者為準) 中的 [內文]。
  
   ![選取內文](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. 從 [對應] 清單中，選取您的 Liquid 範本，在此範例中是「JsonToJsonTemplate」。

   ![選取對應](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果清單是空的，表示邏輯應用程式可能未連結至您的企業整合帳戶。 
   若要將邏輯應用程式連結至具有 Liquid 範本或對應的企業整合帳戶，請遵循下列步驟：

   1. 在企業邏輯應用程式功能表上，選取 [工作流程設定]。
   2. 從 [選取企業整合帳戶] 清單，選取您的企業整合帳戶，並選擇 [儲存]。

   ![將邏輯應用程式連結至企業整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>測試應用程式邏輯

從 [Postman](https://www.getpostman.com/postman) 或類似的工具將 JSON 輸入張貼至邏輯應用程式。 從邏輯應用程式轉換的 JSON 輸出如下列範例所示：
  
![範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>更多 Liquid 動作範例
Liquid 並非限用於 JSON 轉換。 以下其他可使用 Liquid 的轉換動作。

* JSON 轉換為文字
  
  以下是此範例所使用的 Liquid 範本：
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   以下是範例輸入和輸出：
  
   ![JSON 轉換為文字的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML 轉換為 JSON
  
  以下是此範例所使用的 Liquid 範本：
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   以下是範例輸入和輸出：

   ![XML 轉換為 JSON 的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML 轉換為文字
  
  以下是此範例所使用的 Liquid 範本：

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   以下是範例輸入和輸出：

   ![XML 轉換為文字的範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>後續步驟

* [深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "了解企業整合套件")  
* [深入了解對應](../logic-apps/logic-apps-enterprise-integration-maps.md "了解企業整合對應")  

