---
title: "在工作流程中建立迴圈和範圍，或解除批次 (debatch) 資料 - Azure Logic Apps | Microsoft Docs"
description: "在 Azure Logic Apps 中建立迴圈來逐一查看資料、將動作群組為範圍，或者解除批次資料，以便啟動更多工作流程。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9cdbe4a12a0b16341a1e52f176901045baf327b5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Logic Apps 迴圈、範圍和解除批次處理
  
Logic Apps 提供數種方法來處理工作流程中的陣列、集合、批次和迴圈。
  
## <a name="foreach-loop-and-arrays"></a>ForEach 迴圈和陣列
  
Logic Apps 可讓您在一組資料上進行迴圈，並為每個項目執行動作。  在集合上迴圈就可透過`foreach`動作。  在設計工具中，您可以將 for each 迴圈。  在選取您希望反覆查看的陣列之後，就可以新增動作。  您可以每個 foreach 迴圈中新增多個動作。  一次迴圈中，您可以開始指定應該會發生什麼在陣列的每個值。

  這個範例會傳送一封電子郵件包含 'microsoft.com' 每個電子郵件地址。 如果使用程式碼檢視中，您可以指定 for each 迴圈，如下列範例所示：

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A`foreach`動作可以逐一查看陣列上千個實體。  根據預設，平行執行反覆項目。  請參閱[限制以及設定](logic-apps-limits-and-config.md)如陣列和並行限制的詳細資訊。

### <a name="sequential-foreach-loops"></a>循序 ForEach 迴圈

若要啟用要循序執行的 foreach 迴圈，應該新增 `Sequential` 作業選項。

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Until 迴圈
  
  您可以執行某個動作或一系列動作，直到符合某個條件為止。  最常見的案例使用直到迴圈呼叫端點，直到您獲得所需的回應。  在設計工具中，您可以指定要新增一個 until 迴圈。  在迴圈中新增動作之後，您就可以設定結束條件以及迴圈限制。
  
  這個範例會呼叫的 HTTP 端點，直到回應主體具有值 'Completed'。  完成時可能是： 
  
  * HTTP 回應具有「已完成」的狀態
  * 嘗試為一小時
  * 已執行過 100 次的迴圈
  
  如果使用程式碼檢視中，您可以指定 until 迴圈，如下列範例所示：
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn 和解除批次處理

觸發程序有時可能會接收到您想要解除批次處理並針對每個項目啟動工作流程的項目陣列。  此解除批次處理即將可透過完成`spliton`命令。  根據預設，如果觸發程序 swagger 指定屬於陣列、 裝載`spliton`加入。 `spliton`命令啟動陣列中的每個項目執行。  SplitOn 只加入觸發程序，可以手動設定或覆寫。 您無法具有 `spliton` 並同時實作同步回應模式。  呼叫的任何工作流程具有`response`除了動作`spliton`以非同步方式執行，並將傳送立即`202 Accepted`回應。  

  這個範例會接收項目的陣列，並 debatches 上每個資料列。 SplitOn 可以指定程式碼檢視中，如下列範例：

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>範圍

可能可以使用 scope 來將一系列動作群組在一起。  範圍可用於實作例外狀況處理。  在設計工具中，您可以新增範圍，並開始在其內部新增任何動作。  您可以如下列範例程式碼檢視中定義的範圍：


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
