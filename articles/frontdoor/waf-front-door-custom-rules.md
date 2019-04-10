---
title: Web 應用程式防火牆自訂規則的 Azure 大門
description: 了解如何使用 web 應用程式防火牆 (WAF) 自訂規則保護您的 web 應用程式受到惡意的攻擊。
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362958"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>與 Azure 前端的 web 應用程式防火牆的自訂規則
與前端服務的 azure web 應用程式防火牆 (WAF) 可讓您控制存取您的 web 應用程式，根據您定義的條件。 自訂 WAF 規則是由優先順序號碼、 規則類型，比對條件和動作所組成。 有兩種類型的自訂規則： 比對規則和速率限制規則。 比對規則控制存取根據比對條件，而速率限制規則控制存取根據比對條件和內送要求的速率。 您可能會停用自訂的規則，以防止它進行評估，但仍保留組態。 這篇文章討論 http 參數為基礎的比對規則。

## <a name="priority-match-conditions-and-action-types"></a>優先順序、 比對條件和動作類型
您可以控制與自訂 WAf 規則，以定義優先順序號碼、 規則類型，比對條件和動作的存取。 

- **優先順序：** 是唯一的整數，描述的 WAF 規則的評估順序。 較高的值的規則優先評估具有較低值的規則

- **動作：** 定義如何路由傳送要求，如果符合 WAF 規則。 您可以選擇其中一個動作時，適用下列要求會符合自訂規則。

    - *允許*-WAF 會轉送至後端 quest、 將項目記錄在 WAF 記錄檔及結束。
    - *區塊*-要求被封鎖，WAF 會傳送回應給用戶端，而不將要求轉送到後端。 WAF 記錄在 WAF 記錄檔中的項目。
    - *記錄*-WAF 記錄檔中 WAF 的項目記錄，並會繼續評估下一個規則。
    - *重新導向*-WAF 要求重新導向至指定的 URI，將項目記錄在 WAF 記錄檔，並結束。

- **比對條件：** 會定義比對變數是運算子，而符合值。 每個規則都可能包含多個比對條件。 比對條件可能會根據下面*符合變數*:
    - RemoteAddr (用戶端 IP)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **運算子：** 清單包含下列：
    - 任何： 通常用來定義預設動作，如果沒有規則進行比對。 任何是相符項目所有的運算子。
    - IPMatch： 定義 IP 限制 RemoteAddr 變數
    - GeoMatch： 定義地理篩選 RemoteAddr 變數
    - 等於
    - Contains
    - LessThan： 大小條件約束
    - GreaterThan： 大小條件約束
    - LessThanOrEqual： 大小條件約束
    - GreaterThanOrEqual： 大小條件約束
    - BeginsWith
     - EndsWith

您可以設定*否定*條件設為 true，如果應該要否定的條件的結果。

*符合值*定義可能相符項目值的清單。
支援的 HTTP 要求方法的值包括：
- GET
- POST
- PUT
- HEAD
- 刪除
- 鎖定
- 解除鎖定
- 設定檔
- OPTIONS
- PROPFIND
- PROPPATCH
- MKCOL
- 複製
- 移動

## <a name="examples"></a>範例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>以 http 參數為基礎的 WAF 自訂規則範例

以下是範例示範具有兩個比對條件的自訂規則的設定。 要求是從指定的網站訪客來源，所定義和查詢字串不包含 「 密碼 」。

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
封鎖"PUT"方法的範例組態所示，如下所示：

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>大小條件約束

您可以建立自訂規則，指定大小的連入要求的組件上的條件約束。 例如，以下規則會封鎖超過 100 個字元的 Url。

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>後續步驟
- 深入了解[web 應用程式防火牆](waf-overview.md)
- 了解如何[建立 Front Door](quickstart-create-front-door.md)。

