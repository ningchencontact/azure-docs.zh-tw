---
title: 適用于 Azure Front 的 Web 應用程式防火牆自訂規則
description: 瞭解如何使用 web 應用程式防火牆 (WAF) 的自訂規則, 保護 web 應用程式免于遭受惡意攻擊。
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846269"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>使用 Azure Front 的 web 應用程式防火牆自訂規則
具有 Front 開門服務的 Azure web 應用程式防火牆 (WAF) 可讓您根據所定義的條件, 控制對 web 應用程式的存取。 自訂 WAF 規則是由優先順序編號、規則類型、比對條件和動作所組成。 有兩種類型的自訂規則: 符合規則和速率限制規則。 比對規則會根據比對條件控制存取, 而速率限制規則會根據比對條件和連入要求的速率來控制存取。 您可以停用自訂規則, 以防止它被評估, 但仍保留設定。 本文討論以 HTTP 參數為基礎的相符規則。

## <a name="priority-match-conditions-and-action-types"></a>優先順序、相符條件和動作類型
您可以使用自訂的 WAf 規則來控制存取, 以定義優先順序號碼、規則類型、比對條件和動作。 

- **優先順序:** 是唯一的整數, 描述 WAF 規則的評估順序。 值較低的規則會在具有較高值的規則之前進行評估

- **動作:** 定義若符合 WAF 規則, 如何路由傳送要求。 當要求符合自訂規則時, 您可以選擇下列其中一個要套用的動作。

    -  WAF 會將其轉送至後端, 並在 WAF 記錄中記錄專案並結束。
    -  封鎖要求會遭到封鎖, WAF 會將回應傳送至用戶端, 而不會將要求轉送至後端。 WAF 會在 WAF 記錄中記錄一個專案。
    - *記錄*檔 WAF 會在 WAF 記錄中記錄一個專案, 並繼續評估下一個規則。
    -  WAF 會將要求重新導向至指定的 URI, 並在 WAF 記錄中記錄一個專案, 然後結束。

- 比對**條件:** 定義符合變數、運算子和比對值。 每個規則可能包含多個符合條件。 比對條件可以根據下列*符合的變數*:
    - RemoteAddr (用戶端 IP)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operator:** list 包含下列各項:
    - Any: 如果沒有符合規則, 通常會用來定義預設動作。 Any 是 match all 運算子。
    - IPMatch: 定義 RemoteAddr 變數的 IP 限制
    - GeoMatch: 定義 RemoteAddr 變數的地區篩選
    - 等於
    - Contains
    - LessThan: size 條件約束
    - GreaterThan: size 條件約束
    - LessThanOrEqual: size 條件約束
    - GreaterThanOrEqual: size 條件約束
    - 開始于
     - EndsWith

如果條件的結果應該是否定的, 您可以將 [*否定*條件] 設定為 [true]。

*Match 值*定義可能的相符值清單。
支援的 HTTP 要求方法值包括:
- GET
- POST
- PUT
- HEAD
- DELETE
- 狀
- 解除鎖定
- 特徵
- OPTIONS
- PROPFIND
- PROPPATCH
- MKCOL
- 複製
- 進入

## <a name="examples"></a>範例

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>根據 HTTP 參數 WAF 自訂規則範例

以下範例顯示具有兩個比對條件之自訂規則的設定。 要求是來自指定的網站 (如查閱者所定義), 而查詢字串不包含 "password"。

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
封鎖 "PUT" 方法的範例設定如下所示:

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

您可以建立自訂規則, 針對傳入要求的一部分指定大小條件約束。 例如, 下列規則會封鎖長度超過100個字元的 Url。

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
- 深入瞭解[web 應用程式防火牆](waf-overview.md)
- 了解如何[建立 Front Door](quickstart-create-front-door.md)。

