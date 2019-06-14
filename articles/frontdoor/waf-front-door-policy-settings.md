---
title: 與 Azure 前端的 web 應用程式防火牆的原則設定
description: 了解 web 應用程式防火牆 (WAF)。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459363"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>與 Azure 前端的 web 應用程式防火牆的原則設定

Web 應用程式防火牆 (WAF) 原則可讓您一組自訂和受管理的規則來控制存取您的 web 應用程式。 WAF 原則名稱必須是唯一的。 如果您嘗試使用現有的名稱，您會收到驗證錯誤。 有多個原則層級設定會套用至指定該原則，如這篇文章中所述的所有規則。

## <a name="waf-state"></a>WAF 狀態

前端的 WAF 原則可以是下列兩種狀態之一：
- **已啟用：** 啟用原則時，WAF 會主動檢查連入要求，並採取相對應的動作，根據規則定義
- **已停用：** -停用原則時，WAF 檢查已暫停。 連入要求會略過 WAF，並傳送至後端根據大門路由。

## <a name="waf-mode"></a>WAF 模式

在下列兩種模式中執行，就可以設定 WAF 原則：

- **偵測模式**偵測模式中執行時，WAF 不採取任何動作，監視以外和 WAF 記錄檔記錄要求和其相符的 WAF 規則。 開啟記錄診斷的大門 (當使用入口網站，這可藉由移至**診斷**在 Azure 入口網站中的一節)。

- **防止模式**WAF 設定為在防止模式中執行時，採取指定的動作，如果要求符合規則。 WAF 記錄也會記錄任何相符的要求。

## <a name="waf-response-for-blocked-requests"></a>WAF 已封鎖的要求回應

根據預設，當 WAF 會封鎖要求比對的規則，因為它會傳回 403 狀態碼-**要求會被封鎖**訊息。 參考字串也會傳回記錄。

當要求遭到封鎖的 WAF 時，您可以定義自訂的回應狀態碼和回應訊息。 支援下列自訂的狀態碼：

- 200 確定
- 403    Forbidden
- 405 不允許的方法
- 406 無法接受
- 429 太多要求

自訂的回應狀態碼和回應訊息是原則層級設定。 一旦設定之後，所有已封鎖的要求會取得相同的自訂回應狀態和回應訊息。

## <a name="uri-for-redirect-action"></a>重新導向動作的 URI

您所定義的 URI 將要求重新導向，如果**重新導向**WAF 原則中包含的規則的任何選取動作。 此重新導向 URI 必須是有效的 HTTP (S) 站台和設定後，使用 「 重新導向 」 動作的比對規則會重新導向至指定的站台的所有要求。


## <a name="next-steps"></a>後續步驟
- 了解如何定義 WAF[自訂回應](waf-front-door-configure-custom-response-code.md)
