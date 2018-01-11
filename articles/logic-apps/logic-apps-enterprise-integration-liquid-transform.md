---
title: "將不固定的轉型為 Azure 邏輯應用程式的 JSON 資料轉換 |Microsoft 文件"
description: "建立轉換或對應的進階 JSON 轉換使用邏輯應用程式並不固定的範本。"
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: cd177b1ebcb5d236ce265dc153ee6a02125a69df
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="advanced-json-transformations-using-liquid-template"></a>進階的 JSON 轉換使用不固定範本
Azure 邏輯應用程式支援透過原生資料作業動作，例如撰寫或剖析 JSON 的基本 JSON 轉換。 邏輯應用程式現在也支援不固定的範本使用的進階的 JSON 轉換。 [不固定](https://shopify.github.io/liquid/)是一種開放原始碼範本語言有彈性的 web 應用程式。
 
在本文中，了解如何使用不固定的地圖或範本，可支援更複雜的 JSON 轉換，例如反覆項目、 控制流程、 變數和等等。 您必須定義對 JSON 的對應，使用這個不固定的對應的 JSON，並整合帳戶中儲存該對應，才能在邏輯應用程式中執行不固定的轉換。

## <a name="prerequisites"></a>必要條件
以下是使用不固定動作的必要條件：

* Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以[開始使用免費 Azure 帳戶](https://azure.microsoft.com/free/)。 否則，您可以[註冊隨用隨付訂用帳戶](https://azure.microsoft.com/pricing/purchase-options/)。

* 相關的基本知識[如何建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

* 基本[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)。


## <a name="create-and-add-liquid-template-or-map-to-integration-account"></a>建立並新增不固定的範本或對應至整合帳戶

1. 建立此範例中的範例不固定範本。 不固定的範本會定義如何將轉換 JSON 輸入如下所示：

   ```
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
    > 如果您不固定的範本使用任何[篩選](https://shopify.github.io/liquid/basics/introduction/#filters)，這些篩選條件必須以大寫字母開頭。 

2. 登入 [Azure 入口網站](https://portal.azure.com)。

3. 在主要 Azure 功能表中，選擇**所有資源**。 

4. 在 [搜尋] 方塊中，提供您整合的帳戶。 選取您的帳戶。

   ![選取整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  整合帳戶磚中，選取**對應**。

   ![選取的對應](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. 選擇**新增**對應提供這些詳細資料：
  * **名稱**： 您的對應，也就是 「 JsontoJsonTemplate 「 在此範例中的名稱。
  * **型別對應**： 您的對應類型。 For JSON，將 JSON 轉換，您必須選取**不固定**。
  * **地圖**： 現有不固定範本或對應檔案的轉換，也就是 「 SimpleJsonToJsonTemplate.liquid 「 在此範例中使用。 您可以使用檔案選擇器來尋找此檔案。

    ![新增不固定範本](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

    
## <a name="add-the-liquid-action-to-transform-json-in-your-logic-app"></a>加入不固定的動作，將 JSON 轉換邏輯應用程式中

1. [建立邏輯應用程式](logic-apps-create-a-logic-app.md)。

2. 新增[要求觸發程序](../connectors/connectors-native-reqres.md#use-the-http-request-trigger)邏輯應用程式。

3. 選擇**+ 新增步驟 > 將動作加入**。 搜尋*不固定*[搜尋] 方塊中。 選取**液晶-轉換為 JSON 的 JSON**。

  ![搜尋-動作-液晶](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. 在**內容**方塊中，選取**主體**動態內容的清單或參數清單中，兩者會出現。 
  
  ![選取主體](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. 從**對應**下拉式清單中，選取您不固定的範本，在此範例中是 JsonToJsonTemplate。

  ![選取地圖](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   如果清單是空的邏輯應用程式最有可能不會連結到整合帳戶中。 若要整合帳戶具有不固定的範本或對應至連結邏輯應用程式，請遵循下列步驟：

   1. 在邏輯應用程式功能表中，選取**工作流程設定**。 
   2. 從**選取整合帳戶**清單，選取您整合的帳戶，然後選擇 **儲存**。

     ![連結邏輯應用程式整合帳戶](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)


## <a name="test-your-logic-app"></a>測試應用程式邏輯

   張貼至應用程式邏輯，從 JSON 輸入[郵差](https://www.getpostman.com/postman)或類似的工具。 從邏輯應用程式轉換後的 JSON 輸出看起來像本範例中：
  
   ![範例輸出](./media/logic-apps-enterprise-integration-liquid-transform/example-output.png)


## <a name="next-steps"></a>後續步驟
* [深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "了解企業整合套件")  
* [深入了解對應](../logic-apps/logic-apps-enterprise-integration-maps.md "了解企業整合對應")  

