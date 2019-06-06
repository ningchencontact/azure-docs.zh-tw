---
title: 來自 Verizon Premium 的 azure CDN 規則引擎條件運算式 |Microsoft Docs
description: Azure CDN from Verizon Premium 的參考文件規則引擎比對條件和功能。
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: c6e49e6fbc0c541ce9a8cd903eb313d61413257c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481530"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>來自 Verizon Premium 規則引擎條件運算式的 azure CDN

這篇文章列出詳細的說明的條件運算式的 Azure 內容傳遞網路 (CDN)[規則引擎](cdn-verizon-premium-rules-engine.md)。

規則的第一個部分是條件運算式。

條件運算式 | 描述
-----------------------|-------------
IF | IF 運算式永遠是規則中第一個陳述式的一部分。 像所有其他條件運算式一樣，此 IF 陳述式必須與符合項目相關聯。 如果沒有其他的條件式運算式定義，此種比對會判斷一組功能可能會套用至要求之前必須符合的準則。
AND IF | AND IF 運算式只能在下列類型的條件運算式之後新增︰IF、AND IF。 它表示針對初始 IF 陳述式有必須符合的另一個條件。
ELSE IF| ELSE IF 運算式會指定其他條件，必須在此 ELSE IF 陳述式特定的一組功能發生之前符合。 有 ELSE IF 陳述式表示前一個陳述式的結尾。 可以放在 ELSE IF 陳述式之後的條件運算式是另一個 ELSE IF 陳述式。 這表示 ELSE IF 陳述式只能用來指定必須符合的單一其他條件。

**範例**：![CDN 相符條件](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 後一項規則可能會覆寫前一項規則所指定的動作。
   > 範例：全面涵蓋規則會保護所有透過權杖型驗證要求。 可以直接在它底下建立另一個規則，對特定類型的要求產生例外狀況。

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [規則引擎參考](cdn-verizon-premium-rules-engine-reference.md)
- [規則引擎比對條件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [規則引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用規則引擎覆寫預設的 HTTP 行為](cdn-verizon-premium-rules-engine.md)