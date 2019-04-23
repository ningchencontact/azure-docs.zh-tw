---
title: Azure web 應用程式防火牆-常見問題集
description: 此頁面提供有關 Azure 前端服務常見問題的解答
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 05d01851d0a3dc9df6c396e862ce93defd957c70
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788916"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Azure web 應用程式防火牆的常見問題集

這篇文章會回答有關 Azure web 應用程式防火牆 (WAF) 功能與功能的常見問題。 

## <a name="what-is-azure-waf"></a>什麼是 Azure WAF？

Azure 的 WAF，協助保護您的 web 應用程式，從常見的潛在威脅，例如 SQL 插入式攻擊、 跨網站指令碼和其他網路攻擊的 web 應用程式防火牆。 您可以定義自訂和受管理的規則，以控制存取您的 web 應用程式的組合所組成的 WAF 原則。

Azure WAF 原則可以套用至應用程式閘道或 Azure 前端服務上裝載的 web 應用程式中。

## <a name="what-is-waf-for-azure-front-door-service"></a>什麼是 Azure 前端服務的 WAF？ 

Azure 的大門是可高度擴充且全域散發的應用程式和內容傳遞網路。 Azure WAF 的大門，與整合時停止阻絕服務，並為目標的應用程式在 Azure 網路邊緣的攻擊，攻擊來源，進入您的虛擬網路，才能提供保護不需要犧牲效能。

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>如何將收費的大門 Azure waf？
公開預覽期間，在前門 WAF 使用量是免費。 請注意，前端費用額外。 請參閱大門 Service 定價[此處](https://azure.microsoft.com/pricing/details/frontdoor/)。

## <a name="does-azure-waf-support-https"></a>Azure WAF 是否支援 HTTPS？

前端服務會提供 SSL 卸載。 WAF 會與大門原生整合，而且可以檢查要求之後，對它進行解密。

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF 是否支援 IPv6？

是。 您可以設定 IPv4 和 IPv6 IP 限制。

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>如何保持最新狀態是受管理的規則集？

我們盡所能跟上變化的威脅。 新的規則更新之後，會將它新增至預設規則集與新的版本號碼。

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>什麼是傳播時，如果我變更我的 WAF 原則？

部署 WAF 原則全域通常需要大約 5 分鐘，並通常更快完成。

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF 原則都可以不同的區域不同嗎？

與前端服務整合時，WAF 會是全域資源。 相同的組態適用於所有的 「 大門 」 位置。
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>如何限制只有從前端是我端存取？

您可以允許只有大門輸出 IP 位址範圍，並拒絕來自網際網路的任何直接存取後端中設定 IP 存取控制清單。 您可以在虛擬網路上使用支援服務標籤。 此外，您可以確認 X 轉送主機 HTTP 標頭欄位是適用於您的 web 應用程式。




## <a name="which-azure-waf-options-should-i-choose"></a>我應該選擇哪一個 Azure WAF 選項？

套用在 Azure 中的 WAF 原則時，有兩個選項。 WAF 與 Azure 前端是全域散發的邊緣安全性解決方案。 與應用程式閘道 WAF 是區域、 專用的解決方案。 我們建議您選擇根據您的整體效能和安全性需求的解決方案。 如需詳細資訊，請參閱 <<c0> [ 負載平衡與 Azure 應用程式傳遞套件](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>您在所有的整合式平台支援相同的 WAF 功能？

目前，只會支援 ModSec CRS 2.2.9 和 CRS 3.0 規則在應用程式閘道 waf。 速率限制、 地區篩選，以及 Azure 受管理預設規則集規則只支援 WAF 在 Azure 大門。

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS 保護與大門整合？ 

全域散發在 Azure 網路邊緣，Azure 前端可以吸收，並異地隔離大量攻擊。 您可以建立自訂的 WAF 原則，以將區塊自動和速率限制具有已知簽章的 http （s） 攻擊。 更進一步，您可以啟用 DDoS Protection Standard 後端部署所在的 vnet。 Azure DDoS Protection Standard 的客戶會收到其他好處包括在攻擊期間的成本保護、 SLA 保證，以及存取專家 DDoS 快速回應小組立即的說明。 

## <a name="next-steps"></a>後續步驟

- 深入了解[Azure web 應用程式防火牆](waf-overview.md)。
- 深入了解[Azure 大門](front-door-overview.md)。
