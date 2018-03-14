---
title: "使用 JSON 依邏輯應用程式定義建置 - Azure Logic Apps | Microsoft Docs"
description: "使用日期函式新增參數、處理字串、建立參數對應以及取得資料"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 01/31/2018
ms.author: LADocs; estfan
ms.openlocfilehash: d05f7e34cbe670db6733c199e3420c810c304a84
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2018
---
# <a name="build-on-your-logic-app-definition-with-json"></a>使用 JSON 依邏輯應用程式定義建置

若要使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 執行更進階的工作，您可以使用程式碼檢視來編輯使用簡單宣告式 JSON 語言的邏輯應用程式定義。 如果您還沒有這麼做，請先檢閱[如何建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 此外，請參閱[工作流程定義語言的完整參考](http://aka.ms/logicappsdocs)。

> [!NOTE]
> 只有您在程式碼檢視中處理邏輯應用程式的定義時，才可使用某些 Azure Logic Apps 功能 (如參數)。 參數可讓您在整個邏輯應用程式中重複使用值。 例如，如果您需想在數個動作中使用同一個電子郵件地址，請將該電子郵件地址定義為參數。

## <a name="view-and-edit-your-logic-app-definitions-in-json"></a>在 JSON 中檢視和編輯您的邏輯應用程式定義

1. 登入 [Azure 入口網站](https://portal.azure.com "Azure 入口網站")。

2. 在左側功能表中，選擇 [更多服務]。 在 [企業整合] 底下選擇 [Logic Apps]。 選取您的邏輯應用程式。

3. 從邏輯應用程式功能表的 [開發工具] 底下，選擇 [邏輯應用程式程式碼檢視]。

   [程式碼檢視] 視窗隨即開啟，並顯示您的邏輯應用程式定義。

## <a name="parameters"></a>參數

參數可讓您在整個邏輯應用程式中重複使用值，且適用於取代您可能經常變更的值。 例如，如果您想要在多個位置中使用同一個電子郵件地址，您應將該電子郵件地址定義為參數。 

當您必須在不同的環境中覆寫參數時，參數也很有用。深入了解[用於部署的參數](#deployment-parameters)和 [Azure Logic Apps 文件的 REST API](https://docs.microsoft.com/rest/api/logic)。

> [!NOTE]
> 參數僅適用於程式碼檢視。

在[第一個範例邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)中，您所建立的工作流程會在網站的 RSS 摘要中出現新文章時傳送電子郵件。 摘要的 URL 為硬式編碼，因此這個範例會示範如何將查詢值取代為參數，進而更輕鬆地變更摘要的 URL。

1. 在程式碼檢視中，尋找 `parameters : {}` 物件，並新增 `currentFeedUrl` 物件：

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. 在 `When_a_feed-item_is_published` 動作中，尋找 `queries` 區段，然後以 `"feedUrl": "#@{parameters('currentFeedUrl')}"` 取代查詢值。 

   **之前**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **之後**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   若要加入兩或多個字串，您也可以使用 `concat` 函式。 
   例如，`"@concat('#',parameters('currentFeedUrl'))"` 的運作方式與上述範例相同。

3.  完成之後，請選擇 [儲存]。 

現在您可以透過 `currentFeedURL` 物件傳遞不同的 URL，藉以變更網站的 RSS 摘要。

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>不同環境的部署參數

通常，部署生命週期具有開發、預備及生產的環境。 例如，您可以在所有這些環境中使用相同的邏輯應用程式定義，但使用不同的資料庫。 同樣地，建議您在不同的區域使用相同的定義，以發揮高可用性，但希望每個邏輯應用程式執行個體使用該區域的資料庫。 

> [!NOTE] 
> 這種情況與在執行階段採用參數不同，您反而應該使用 `trigger()` 函式。

以下是基本定義：

``` json
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
在邏輯應用程式的實際 `PUT` 要求中，您可以提供參數 `uri`。 在每個環境中，您可以提供不同的值給 `connection` 參數。 因為預設值不存在，邏輯應用程式承載需要此參數︰

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

若要深入了解，請參閱 [Azure Logic Apps 文件的 REST API](https://docs.microsoft.com/rest/api/logic/)。

## <a name="process-strings-with-functions"></a>使用函式處理字串

Logic Apps 具有各種函式可處理字串。 例如，假設您需要將公司名稱從訂單傳遞至另一個系統。 不過，您不確定字元編碼的正確處理方式。 您可以在這個字串上執行 base64 編碼，但若要避免在 URL 中逸出，您可改為取代數個字元。 此外，公司名稱只需要一個子字串，因為不會用到前五個字元。 

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

下列步驟說明此範例從內部到外部處理這個字串的方式：

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. 取得公司名稱的 [`length()`](../logic-apps/logic-apps-workflow-definition-language.md)，以便取得字元總數。

2. 若要取得較短的字串，請減去 `5`。

3. 現在可取得 [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)。 從索引 `5` 開始，並移至字串的其餘部分。

4. 將這個子字串轉換成 [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) 字串。

5. 現在以 `-` 字元 [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) 所有 `+` 字元。

6. 最後，以 `_` 字元 [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) 所有 `/` 字元。

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>將清單項目對應到屬性值，然後使用對應作為參數

若要根據屬性值取得不同的結果，您可以建立可比對每個屬性值與結果的對應，然後使用該對應作為參數。 

例如，此工作流程會將某些類別定義為參數，以及定義可比對這些類別與特定 URL 的對應。 首先，工作流程會取得文章清單。 接著，工作流程會使用此對應來尋找符合每篇文章之類別的 URL。

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) 函式會檢查類別是否符合已定義的知名類別。

*   取得相符的類別之後，此範例會使用方括號來提取對應中的項目：`parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>使用 Date 函式取得資料

若要從原生不支援「觸發程序」的資料來源取得資料，您可改為使用 Date 函式來處理時間和日期。 例如，這個運算式會尋找這個工作流程從內部到外部的步驟需要花費多少時間：

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. 從 `order` 動作，擷取 `startTime`。 
2. 使用 `utcNow()` 取得目前時間。
3. 減去一秒：

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   您可以使用其他的時間單位，例如 `minutes` 或 `hours`。 

3. 現在，您可以比較這兩個值。 

   如果第一個值小於第二個值，則自從訂單最初提交以來已超過一秒。

若要將日期格式化，您可以使用字串格式器。 例如，若要取得 RFC1123，請使用 [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md)。 深入了解[日期格式化](../logic-apps/logic-apps-workflow-definition-language.md)。

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```


## <a name="next-steps"></a>後續步驟

* [根據條件 (條件陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [根據不同的值 (Switch 陳述式) 執行步驟](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [執行並重複步驟 (迴圈)](../logic-apps/logic-apps-control-flow-loops.md)
* [執行或合併平行步驟 (分支)](../logic-apps/logic-apps-control-flow-branches.md)
* [根據分組的動作狀態執行步驟 (範圍)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* 深入了解 [Azure Logic Apps 的工作流程定義語言結構描述](../logic-apps/logic-apps-workflow-definition-language.md)
* 深入了解 [Azure Logic Apps 的工作流程動作和觸發程序](../logic-apps/logic-apps-workflow-actions-triggers.md)