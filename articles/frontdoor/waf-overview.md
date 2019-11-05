---
title: 什麼是適用于 Azure Front 的 Azure web 應用程式防火牆？ (預覽)
description: 瞭解適用于 Azure Front 服務的 Azure web 應用程式防火牆如何保護您的 web 應用程式免于遭受惡意攻擊。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 624a5af59a39cb0ebf647f549f3a40ed56f78b9e
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73549244"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>什麼是適用于 Azure Front 的 Azure web 應用程式防火牆？

Azure Web 應用程式防火牆 (WAF) 會以集中保護的方式，保護透過使用 Azure Front Door 向全球提供的 Web 應用程式。 其作用是協助 Web 服務抵禦常見的惡意攻擊和弱點，並為您的使用者維持服務的高可用性，以及協助您符合法規需求。


Web 應用程式逐漸增加了惡意攻擊的目標，例如阻絕服務氾濫、SQL 插入式攻擊和跨網站腳本攻擊。 這些惡意攻擊可能會導致服務中斷和資料遺失，對 web 應用程式擁有者造成嚴重的威脅。

想要防止應用程式的程式碼受到這類攻擊會非常困難，而且可能需要對多層次的應用程式拓撲執行嚴格的維護、修補和監視工作。 集中式 Web 應用程式防火牆有助於簡化安全性管理作業，且更加確保應用程式管理員能夠對抗威脅或入侵。 此外，WAF 解決方案可以在中央位置修補已知弱點，更快地回應安全性威脅，而不是保護每個個別的 web 應用程式。

WAF for Front 是一個全域且集中式的解決方案。 它會部署在全球的 Azure 網路邊緣位置，並在網路邊緣檢查 WAF 啟用之 web 應用程式的每個傳入要求。 這可讓 WAF 在進入您的虛擬網路並提供大規模的全域保護，而不犧牲效能的情況下，防止惡意攻擊接近攻擊來源。 WAF 原則可以輕鬆地連結至您訂用帳戶中的任何 Front 設定檔，而且可以在幾分鐘內部署新規則，讓您快速回應變更的威脅模式。

![Azure web 應用程式防火牆](./media/waf-overview/web-application-firewall-overview.png)

您也可以使用應用程式閘道來啟用 Azure WAF。 如需詳細資訊，請參閱[Web 應用程式防火牆](../application-gateway/waf-overview.md)。

## <a name="waf-policy-and-rules"></a>WAF 原則和規則

您可以設定 WAF 原則，並將該原則與一或多個前端前端以進行保護。 WAF 原則包含兩種類型的安全性規則：

- 客戶所撰寫的自訂規則。

- 受控規則集，是由 Azure 管理且預先設定的一組規則集合。

當兩者都存在時，會先處理自訂規則，然後再處理受管理規則集內的規則。 規則是由比對條件、優先順序和動作組成。 支援的動作類型為：允許、封鎖、記錄和重新導向。 您可以結合受控和自訂規則，以建立符合特定應用程式保護需求的完全自訂原則。

原則中的規則會依照優先順序來處理，其中 priority 是定義要處理之規則順序的唯一整數。 較小的整數值表示較高的優先順序，而且會在具有較高整數值的規則之前進行評估。 一旦符合規則，規則中所定義的對應動作就會套用至要求。 一旦處理這種相符的結果，將不會進一步處理優先順序較低的規則。

Front 門板所傳遞的 web 應用程式一次只能有一個相關聯的 WAF 原則。 不過，您可以擁有 Front 設定，而不會有任何相關聯的 WAF 原則。 如果有 WAF 原則，則會將它複寫到我們所有的邊緣位置，以確保全球安全性原則的一致性。

## <a name="waf-modes"></a>WAF 模式

WAF 原則可以設定為在下列兩種模式中執行：

- **偵測模式：** 在偵測模式中執行時，WAF 不會接受監視器以外的其他任何動作，並將要求和其相符的 WAF 規則記錄到 WAF 記錄。 您可以開啟前門的記錄診斷（使用入口網站時，可以前往 Azure 入口網站中的 **診斷** 區段達成此目的）。

- **預防模式：** 當設定為在防止模式中執行時，如果要求符合規則，而且如果找到相符專案，則 WAF 會接受指定的動作，而不會評估任何較低優先順序的規則。 任何相符的要求也會記錄在 WAF 記錄中。

## <a name="waf-actions"></a>WAF 動作

當要求符合規則的條件時，WAF 客戶可以選擇從其中一個動作執行：

- **允許：** 要求通過 WAF，並轉送至後端。 任何較低優先順序的規則都不會封鎖此要求。
- **封鎖：** 要求會遭到封鎖，而 WAF 會將回應傳送至用戶端，而不會將要求轉送至後端。
- **記錄檔：** 要求會記錄在 WAF 記錄中，而 WAF 會繼續評估較低優先順序的規則。
- 重新**導向：** WAF 會將要求重新導向至指定的 URI。 指定的 URI 是原則層級設定。 一旦設定之後，所有符合重新**導向**動作的要求都會傳送至該 URI。

## <a name="waf-rules"></a>WAF 規則

WAF 原則可以包含兩種類型的安全性規則-自訂規則，由客戶和受控規則集所撰寫，Azure 管理的預先設定規則組。

### <a name="custom-authored-rules"></a>自訂撰寫的規則

您可以設定自訂規則 WAF，如下所示：

- **IP 允許清單和封鎖清單：** 您可以根據用戶端 IP 位址或 IP 位址範圍的清單，設定自訂規則以控制對 web 應用程式的存取。 IPv4 和 IPv6 網址類別型都受到支援。 此清單可設定為封鎖或允許來源 IP 符合清單中 IP 的要求。 用戶端 IP 位址可能與 IP 位址 WAF 觀察不同，例如，當用戶端透過 proxy 存取 WAF 時。 您可以根據用戶端 IP 位址（RemoteAddr）或 WAF （SocketAddr）所看到的 IP 位址，建立[ip 限制規則](https://docs.microsoft.com/azure/frontdoor/waf-front-door-configure-ip-restriction)。 目前使用 Powershell 和 Azure CLI 支援設定 SocketAddr IP 限制規則。

- 以**地理位置為基礎的存取控制：** 您可以設定自訂規則，根據與用戶端 IP 位址相關聯的國家/地區代碼，控制對 web 應用程式的存取。

- 以**HTTP 參數為基礎的存取控制：** 您可以根據符合 HTTP/HTTPS 要求參數的字串（例如查詢字串、POST 引數、要求 URI、要求標頭和要求本文）來設定自訂規則。

- **要求以方法為基礎的存取控制：** 您可以根據要求的 HTTP 要求方法（例如 GET、PUT 或 HEAD）來設定自訂規則。

- **大小條件約束：** 您可以根據要求的特定部分長度（例如查詢字串、Uri 或要求本文）來設定自訂規則。

- **速率限制規則：** 速率控制規則是為了限制來自任何用戶端 IP 的異常高流量。 您可以在一分鐘的期間內，設定允許來自用戶端 IP 的 web 要求數目閾值。 請注意，超過閾值的其他要求可能會在更新要求計數時通過。 速率限制可以與其他比對條件結合，例如 HTTP （S）參數比對，以進行細微的速率控制。

### <a name="azure-managed-rule-sets"></a>Azure 管理的規則集

Azure 管理的規則集提供簡單的方法，讓您針對一組常見的安全性威脅來部署保護。 因為這類規則集是由 Azure 所管理，所以會視需要更新規則，以防止新的攻擊簽章。 在公開預覽版本中，受 Azure 管理的預設規則集會包含下列威脅類別的規則：

- 跨網站腳本
- JAVA 攻擊
- 本機檔案包含
- PHP 插入式攻擊
- 遠端命令執行
- 包含遠端檔案
- 會話 fixation
- SQL 插入式攻擊保護
- 通訊協定攻擊者

當新的攻擊簽章新增至規則集時，預設規則集的版本號碼將會遞增。
預設規則集會在 WAF 原則的偵測模式中啟用。 您可以停用或啟用預設規則集內的個別規則，以符合您的應用程式需求。 您也可以設定每個規則的特定動作（允許/封鎖/重新導向/記錄）。 預設動作是 [封鎖]。 此外，如果您想要略過預設規則集中的任何預先設定規則，也可以在相同的 WAF 原則中設定自訂規則。
在評估預設規則集的規則之前，一律會套用自訂規則。 如果要求符合自訂規則，則會套用對應的規則動作，而要求會遭到封鎖或傳遞至後端，而不會叫用任何進一步的自訂規則或預設規則集中的規則。 此外，您可以選擇從 WAF 原則中移除預設規則集。


### <a name="bot-protection-rule-preview"></a>Bot 保護規則（預覽）

您可以為您的 WAF 啟用受管理的 Bot 保護規則集，以對已知 Bot 類別的要求採取自訂動作。
支援的 bot 類別有3種：錯誤的 bot、良好的 bot 和不明的 bot。 在 [錯誤的 bot] 類別中，有一個規則會根據 clieny 位址的 IP 信譽來偵測惡意 bot。 IP 信譽源自 Microsoft 威脅情報摘要。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)支援 Microsoft 威脅情報，並由多項服務所使用，包括 Azure 資訊安全中心。 此外，假設為已知搜尋引擎的要求也會偵測為惡意。
良好的 bot 是經過驗證的 seacrh 引擎。 不明的類別包含要求會識別為 bot，但具有不明的意圖。 您可以設定自訂動作，以針對不同的 Bot 類別進行封鎖、允許、記錄或重新導向。

![Bot 保護規則集](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，且提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。  如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果已啟用 Bot 保護，則會在 FrontdoorWebApplicationFirewallLog 記錄檔記錄符合 bot 規則的傳入要求。 您可以從儲存體帳戶、事件中樞或 log analytics 存取 WAF 記錄。 

## <a name="configuration"></a>組態

使用 Azure 入口網站、REST Api、Azure Resource Manager 範本和 Azure PowerShell，完全支援設定和部署所有 WAF 規則類型。

## <a name="monitoring"></a>監控

在 Front 中監視 WAF 會與 Azure 監視器整合，以追蹤警示並輕鬆地監視流量趨勢。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 Azure 入口網站來設定 Front 門板的 WAF 原則](waf-front-door-create-portal.md)
