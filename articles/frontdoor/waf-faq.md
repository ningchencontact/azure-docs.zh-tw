---
title: Azure web 應用程式防火牆-常見問題
description: 此頁面提供 Azure Front 服務常見問題的解答
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: f6e0ea7ca5b9b131a8d7d7c2c6546130a7d020b3
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302840"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Azure web 應用程式防火牆的常見問題

本文會回答有關 Azure web 應用程式防火牆（WAF）特性和功能的常見問題。 

## <a name="what-is-azure-waf"></a>什麼是 Azure WAF？

Azure WAF 是一種 web 應用程式防火牆，可協助保護您的 web 應用程式免于遭受常見的威脅，例如 SQL 插入式攻擊、跨網站腳本和其他 web 惡意探索。 您可以定義由自訂和受控規則組合所組成的 WAF 原則，以控制對 web 應用程式的存取。

Azure WAF 原則可以套用至裝載于應用程式閘道或 Azure Front 門板服務上的 web 應用程式。

## <a name="what-is-waf-for-azure-front-door-service"></a>什麼是適用于 Azure Front 服務的 WAF？ 

Azure Front 大門是可高度擴充、全域散發的應用程式和內容傳遞網路。 當 Azure WAF 與 Azure Front 門板整合時，拒絕服務和目標應用程式的攻擊會在 Azure 網路的邊緣停止。 這項保護會出現在攻擊來源附近，才有機會進入您的 vitrual 網路。 適用于 Azure Front 服務的 WAF 提供保護，而不會犧牲效能。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支援 HTTPS？

Front 開門服務提供 SSL 卸載。 WAF 原本就與前門整合，而且可以在解密後檢查要求。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支援 IPv6？

是的。 您可以設定 IPv4 和 IPv6 的 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>受控規則集的最新狀態為何？

我們會盡力掌握不斷變化的威脅環境。 更新新的規則之後，它就會新增至具有新版本號碼的預設規則集。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>如果我對 WAF 原則進行變更，傳播時間是什麼？

全域部署 WAF 原則通常需要大約5分鐘的時間，而且通常會更快完成。

## <a name="can-waf-policies-be-different-for-different-regions"></a>不同區域的 WAF 原則可以有所不同嗎？

與 Front 門板服務整合時，WAF 是全域資源。 相同的設定會套用到所有的 Front 門位置。
 

## <a name="which-azure-waf-options-should-i-choose"></a>我應該選擇哪些 Azure WAF 選項？

在 Azure 中套用 WAF 原則時，有兩個選項。 使用 Azure Front WAF 的是全域散發的邊緣安全性解決方案。 具有應用程式閘道的 WAF 是區域專屬的解決方案。 我們建議您根據整體效能和安全性需求來選擇解決方案。 如需詳細資訊，請參閱[使用 Azure 的應用程式傳遞套件進行負載平衡](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>所有整合式平臺都支援相同的 WAF 功能嗎？

目前，只有應用程式閘道的 WAF 支援 ModSec CRS 2.2.9 和 CRS 3.0 規則。 只有在 Azure Front WAF 才支援速率限制、地區篩選和 Azure 受控預設規則集規則。

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 保護是否與 Front 整合？ 

Azure Front 大門會在 Azure 網路邊緣進行全域散發，以吸收和地理位置隔離大型磁片區攻擊。 您可以建立自訂 WAF 原則，以自動封鎖具有已知簽章的 HTTP （s）攻擊並對其進行速率限制。 此外，您可以在部署後端的 VNet 上啟用 DDoS 保護標準。 Azure DDoS 保護標準客戶享有額外的好處，包括成本保護、SLA 保證，以及從 DDoS 快速回應小組存取專家，以在攻擊期間立即提供協助。 

我們建議您在生產環境中鎖定您的後端，以減少 DDoS 攻擊面。 請參閱[如何? 鎖定對我的後端的存取僅限 Azure Front 大門嗎？](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)


## <a name="next-steps"></a>後續步驟

- 了解 [Azure Web 應用程式防火牆](waf-overview.md)。
- 深入瞭解[Azure Front 大門](front-door-overview.md)。
