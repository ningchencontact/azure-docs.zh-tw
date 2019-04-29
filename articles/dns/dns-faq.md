---
title: Azure DNS 常見問題集
description: 關於 Azure DNS 的常見問題集
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 4f0800dfd264059e1dc8aac32a54f216f777647f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096162"
---
# <a name="azure-dns-faq"></a>Azure DNS 常見問題集

## <a name="about-azure-dns"></a>關於 Azure DNS

### <a name="what-is-azure-dns"></a>什麼是 Azure DNS？

網域名稱系統 (DNS) 可將網站或服務名稱轉譯 (或解析) 為其 IP 位址。 Azure DNS 是適用於 DNS 網域的主機服務。 其使用 Microsoft Azure 基礎結構來提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。

Azure DNS 中的 DNS 網域裝載於 Azure 的 DNS 名稱伺服器全球網路。 此系統會使用「任一傳播」網路，所以每個 DNS 查詢是由最接近的可用 DNS 伺服器回答。 Azure DNS 能為您的網域提供快速的效能與高可用性。

Azure DNS 是架構在 Azure Resource Manager 上。 Azure DNS 可得益於 Resource Manager 的功能，如角色型存取控制、稽核記錄和資源鎖定。 您可以透過 Azure 入口網站、Azure PowerShell Cmdlet 和跨平台 Azure CLI 來管理網域和記錄。 需要自動化 DNS 管理的應用程式可以透過 REST API 和 SDK 與服務進行整合。

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS 的費用是多少？

Azure DNS 計費模型所根據的是 Azure DNS 中裝載的 DNS 區域數量。 此外，也會根據其所接收的 DNS 查詢數量。 根據使用量會提供折扣。

如需詳細資訊，請參閱 [Azure DNS 定價頁面](https://azure.microsoft.com/pricing/details/dns/)。

### <a name="what-is-the-sla-for-azure-dns"></a>Azure DNS 有怎樣的 SLA？

Azure 保證有效的 DNS 要求 100% 的時間都至少會從一部 Azure DNS 名稱伺服器收到回應。

如需詳細資訊，請參閱 [Azure DNS SLA 頁面](https://azure.microsoft.com/support/legal/sla/dns)。

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>什麼是 DNS 區域？ 它與 DNS 網域相同嗎？ 

網域是網域名稱系統中的唯一名稱。 例如 contoso.com。

DNS 區域用來裝載特定網域的 DNS 記錄。 例如，contoso.com 網域可能包含數筆 DNS 記錄。 这些记录可能包含 mail.contoso.com（用于邮件服务器）和 www\.contoso.com（用于网站）。 這些記錄會裝載在 contoso.com DNS 區域中。

網域名稱「只是一個名稱」。 DNS 區域則是包含網域名稱 DNS 記錄的資料資源。 您可以使用 Azure DNS 來裝載 DNS 區域，並在 Azure 中管理網域的 DNS 記錄。 它也提供 DNS 名稱伺服器以回應來自網際網路的 DNS 查詢。

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>我是否必須先購買 DNS 網域名稱才能使用 Azure DNS？ 

不一定。

您無須購買網域，就能將 DNS 區域裝載在 Azure DNS 中。 您無須擁有網域名稱，即可隨時建立 DNS 區域。 此區域的 DNS 查詢只有在導向到指派給此區域的 Azure DNS 名稱伺服器時，才會進行解析。

若要將 DNS 區域連結到全球 DNS 階層，您必須購買網域名稱。 然後，來自世界各地的 DNS 查詢就能找到 DNS 區域，並以 DNS 記錄做出回應。

## <a name="azure-dns-features"></a>Azure DNS 功能

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>使用流量管理員針對網域名稱頂點使用別名記錄時，是否有任何限制？

是。 您必須使用靜態公用 IP 位址來搭配 Azure 流量管理員。 使用靜態 IP 位址設定**外部端點**目標。 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS 是否支援 DNS 型流量路由或端點容錯移轉？

DNS 型流量路由和端點容錯移轉會由流量管理員提供。 流量管理員是一項可以與 Azure DNS 搭配使用的個別 Azure 服務。 如需詳細資訊，請參閱[流量管理員概觀](../traffic-manager/traffic-manager-overview.md)。

Azure DNS 僅支援裝載靜態 DNS 網域，其中所指定 DNS 記錄的每個 DNS 查詢一律會收到相同的 DNS 回應。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS 是否支援網域名稱註冊？

沒有。 Azure DNS 目前不支援購買網域名稱的選項。 若要購買網域，您必須使用第三方網域名稱註冊機構。 註冊機構通常會收取些微年費。 然後，您就可以在 Azure DNS 裝載這些網域來管理 DNS 記錄。 如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

系統會在 Azure 待辦項目中，追蹤用來購買網域名稱的功能。 請使用意見反應網站來[註冊此功能的支援](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar) \(英文\)。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS 是否支援 DNSSEC？

沒有。 Azure DNS 目前不支援網域名稱系統安全性延伸模組 (DNSSEC)。

系統會在 Azure DNS 待辦項目中追蹤 DNSSEC 功能。 請使用意見反應網站來[註冊此功能的支援](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support) \(英文\)。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS 是否支援區域傳輸 (AXFR/IXFR)？

沒有。 Azure DNS 目前不支援區域傳輸。 您可以[使用 Azure CLI 將 DNS 區域匯入到 Azure DNS 中](dns-import-export.md)。 透過 [Azure DNS 管理入口網站](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/az.dns)、[SDK](dns-sdk.md)、[PowerShell Cmdlet](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)，即可管理 DNS 記錄。

系統會在 Azure DNS 待辦項目中追蹤區域傳輸功能。 請使用意見反應網站來[註冊此功能的支援](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c) \(英文\)。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS 是否支援 URL 重新導向？

沒有。 URL 重新導向服務不是 DNS 服務。 它們是在 HTTP 層級運作，而不是在 DNS 層級。 有些 DNS 提供者會將 URL 重新導向服務結合成其整體供應項目的一部分。 但 Azure DNS 目前不支援此服務。

系統會在 Azure DNS 待辦項目中追蹤 URL 重新導向功能。 請使用意見反應網站來[註冊此功能的支援](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape) \(英文\)。

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Azure DNS 是否支援在 TXT 記錄集中使用延伸的 ASCII 編碼集 (8 位元)？

是。 Azure DNS 支援在 TXT 記錄集中使用延伸的 ASCII 編碼集。 但是，您必須使用最新版的 Azure REST API、SDK、PowerShell 和 CLI。 2017 年 10 月 1 日 (或 SDK 2.1) 之前的版本不支援延伸的 ASCII 編碼集。 

例如，您可能會提供字串來作為具有延伸 ASCII 字元 \128 的 TXT 記錄值。 例如，"abcd\128efgh"。 Azure DNS 會在內部表示法中使用這個字元的位元組值，也就是 128。 在解析 DNS 時，此位元組值會在回應中傳回。 也請注意，就解析而言，"abc" 和 "\097\098\099" 是可互換的。 

針對 TXT 記錄，我們遵循 [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) 中的區域檔案主要格式逸出規則。 例如，根據 RFC，`\` 現在實際上會逸出所有東西。 如果您指定 `A\B` 作為 TXT 記錄值，它就只會表示及解析為 `AB`。 如果您真的想讓 TXT 記錄解析成 `A\B`，則需要將 `\` 再次逸出。 例如，指定 `A\\B`。

此支援目前不適用於從 Azure 入口網站所建立的 TXT 記錄。

## <a name="alias-records"></a>別名記錄

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>別名記錄可在哪些案例中派上用場？

請參閱 [Azure DNS 別名記錄概觀](dns-alias.md)中的案例一節。

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>別名記錄集支援哪些記錄類型？

Azure DNS 區域中的下列記錄類型支援別名記錄集：
 
- 具有使用  
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>哪些資源可支援作為別名記錄集的目標？

- **從 DNS A/AAAA 記錄集指向公用 IP 資源**。 您可以建立 A/AAAA 記錄集，並使其成為別名記錄集來指向公用 IP 資源。
- **從 DNS A/AAAA/CNAME 記錄集指向流量管理員設定檔。** 您可以從 DNS CNAME 記錄集指向流量管理員設定檔的 CNAME。 例如，contoso.trafficmanager.net。 現在，您也可以從 DNS 區域中的 A 或 AAAA 記錄集，指向具有外部端點的流量管理員設定檔。
- **指向 Azure 內容傳遞網路 (CDN) 端點**。 當您建立使用 Azure 儲存體和 Azure CDN 的靜態網站時，這非常有用。
- **指向相同區域中的另一個 DNS 記錄集**。 別名記錄可以參考其他相同類型的記錄集。 例如，DNS CNAME 記錄集可以是相同類型的另一個 CNAME 記錄集所用的別名。 如果您想要讓某些記錄集成為別名，某些成為非別名，便可使用這種安排。

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>我是否可從 Azure 入口網站建立及更新別名記錄？

是。 您可以在 Azure 入口網站以及 Azure REST API、PowerShell、CLI 與 SDK 中建立或管理別名記錄。

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>當刪除底層公用 IP 時，別名記錄是否會協助確保我的 DNS 記錄集也會刪除？

是。 此功能是別名記錄的核心功能之一。 其可協助您避免應用程式使用者發生可能的中斷。

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>當底層公用 IP 位址變更時，別名記錄是否會協助確保我的 DNS 記錄集更新為正確的 IP 位址？

是。 此功能是別名記錄的核心功能之一。 其可協助您避免應用程式發生可能的中斷或安全性風險。

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>針對 A 或 AAAA 記錄指向流量管理員使用別名記錄集時是否有任何限制？

是。 若要指向流量管理員設定檔，作為來自 A 或 AAAA 記錄集的別名，則流量管理員設定檔必須只使用外部端點。 當您在流量管理員中建立外部端點時，請提供端點的實際 IP 位址。

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>使用別名記錄需要額外付費嗎？

必須有別名記錄才是有效的 DNS 記錄集。 別名記錄沒有額外費用。

## <a name="use-azure-dns"></a>使用 Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>是否可以使用 Azure DNS 和其他 DNS 提供程序共同托管域？

是。 Azure DNS 支援與其他 DNS 服務共同裝載網域。

若要设置共同托管，请将域的 NS 记录修改为指向这两个提供程序的名称服务器。 名稱伺服器 (NS) 記錄可控制哪些提供者會收到網域的 DNS 查詢。 您可以在 Azure DNS 中、在另一個提供者中，以及在父區域中修改這些 NS 記錄。 父區域通常會透過網域名稱註冊機構來設定。 如需有關 DNS 委派的詳細資訊，請參閱 [DNS 網域委派](dns-domain-delegation.md)。

此外，請確保網域的 DNS 記錄在這兩個 DNS 提供者之間保持同步。 Azure DNS 目前不支援 DNS 區域傳輸。 您必須使用 [Azure DNS 管理入口網站](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/az.dns) \(英文\)、[SDK](dns-sdk.md)、[PowerShell Cmdlet](dns-operations-recordsets.md) 或 [CLI 工具](dns-operations-recordsets-cli.md)，來同步處理 DNS 記錄。

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>我是否必須將我的網域委派給全部四個 Azure DNS 名稱伺服器？

是。 Azure DNS 會為每個 DNS 區域指派四個名稱伺服器。 這樣的安排是為了隔離錯誤和提升復原能力。 若要符合 Azure DNS SLA 的要求，請將您的網域委派給這所有四個名稱伺服器。

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS 在使用量上有什麼限制？

使用 Azure DNS 時，會適用下列的預設限制。

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>我是否可以在資源群組之間或訂用帳戶之間移動 Azure DNS 區域？

是。 您可以在資源群組之間或訂用帳戶之間移動 DNS 區域。

移動 DNS 區域時，對 DNS 查詢並沒有影響。 指派給該區域的名稱伺服器還是一樣。 系統會將 DNS 查詢當作一般輸送量來處理。

如需有關如何移動 DNS 區域的詳細資訊和指示，請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS 變更需要多長的時間才會生效？

新的 DNS 區域和 DNS 記錄通常很快就會出現在 Azure DNS 名稱伺服器上。 時間只要幾秒鐘。

對現有 DNS 記錄所做的變更則可能會久一點。 其通常會在 60 秒內出現在 Azure DNS 名稱伺服器上。 DNS 用戶端所進行的 DNS 快取和 Azure DNS 外的 DNS 遞迴解析程式也可能影響時間。 若要控制這個快取持續時間，請使用每一個記錄集的「存留時間」(TTL) 屬性。

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>我要如何保護我的 DNS 區域免於遭到意外刪除？

Azure DNS 的管理是使用 Azure Resource Manager 來進行的。 Azure DNS 可享有 Azure Resource Manager 所提供的存取控制功能優點。 角色型存取控制可控制哪些使用者具備 DNS 區域和記錄集的讀取或寫入存取權。 資源鎖定可防止意外修改或刪除 DNS 區域和記錄集。

如需詳細資訊，請參閱[保護 DNS 區域和記錄](dns-protect-zones-recordsets.md)。

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>我要如何設定 Azure DNS 中的 SPF 記錄？

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Azure DNS Nameserver 是否需透過 IPv6 解析？ 

是。 Azure DNS 名稱伺服器是雙重堆疊。 雙重堆疊表示其同時具有 IPv4 和 IPv6 位址。 若要尋找指派至您 DNS 區域的 Azure DNS nameservers IPv6 位址，請使用 nslookup 之類的工具。 例如 `nslookup -q=aaaa <Azure DNS Nameserver>`。

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>我要如何設定 Azure DNS 中的 IDN？

國際網域名稱 (IDN) 會使用 [Punycode](https://en.wikipedia.org/wiki/Punycode) 將每個 DNS 名稱編碼。 建立 DNS 查詢時，會使用這些以 Punycode 編碼的名稱來建立。

若要設定 Azure DNS 中的 IDN，請將區域名稱或記錄集名稱轉換成 Punycode。 Azure DNS 目前不支援以 Punycode 作為轉換目標或來源的內建轉換。

## <a name="private-dns"></a>私人 DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支援私人網域？

私人網域的支援是使用私人區域功能進行實作。 此功能目前以公開預覽的形式提供。 私人區域是使用與網際網路面向 Azure DNS 區域相同的工具來進行管理的。 但只能從您指定的虛擬網路內將它們進行解析。 如需詳細資訊，請參閱[概觀](private-dns-overview.md)。

目前 Azure 入口網站上並不支援私人區域。

如需有關 Azure 中其他內部 DNS 選項的資訊，請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

### <a name="whats-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>在私人區域的內容下，註冊虛擬網路和解析虛擬網路之間的差異為何？

您可以將虛擬網路連結至 DNS 私人區域以作為註冊虛擬網路或作為解析虛擬網路。 不管是用哪一種方式，虛擬網路中的虛擬機器都能夠針對私人區域中的記錄成功地進行解析。 若使用註冊虛擬網路，DNS 記錄都會自動註冊到虛擬網路中虛擬機器的區域。 當註冊虛擬網路中的虛擬機器遭到刪除時，來自所連結私人區域的對應 DNS 記錄也會自動遭到移除。 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS 私人區域是否能跨 Azure 區域運作？

是。 私人區域支援在跨 Azure 區域的虛擬網路之間進行 DNS 解析。 就算是沒有明確對虛擬網路進行對等，私人區域也能運作。 所有虛擬網路都必須針對私人區域指定為解析虛擬網路。 客戶可能需要對虛擬網路進行對等，來使 TCP/HTTP 流量能夠從某個區域流至另一個區域。

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>針對私人區域，虛擬網路是否需要能夠連線至網際網路？

沒有。 私人區域能與虛擬網路搭配運作。 客戶可以使用私人區域在虛擬網路內及虛擬網路之間管理虛擬機器或其他資源的網域。 名稱解析並不需要任何網際網路連線能力。 

### <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>相同的私人區域是否可以用於數個虛擬網路以進行解析？

是。 客戶可以將最多 10 個解析虛擬網路關聯至單一私人區域。

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>是否可以將屬於不同訂用帳戶的虛擬網路，作為解析虛擬網路新增至私人區域？

是。 您必須在虛擬網路及私人 DNS 區域上皆擁有寫入作業權限。 寫入權限可授與給數個 RBAC 角色。 例如，傳統網路參與者 RBAC 角色便具有虛擬網路的寫入權限。 如需 RBAC 角色的詳細資訊，請參閱[角色型存取控制](../role-based-access-control/overview.md)。

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>當客戶刪除虛擬機器時，是否也會自動刪除私人區域中之自動註冊的虛擬機器 DNS 記錄？

是。 如果您刪除註冊虛擬網路內的虛擬機器，註冊至該區域的 DNS 記錄也會自動刪除。 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>來自註冊虛擬網路的私人區域中所自動註冊的虛擬機器記錄是否可以手動刪除？

沒有。 從註冊虛擬網路自動註冊於私人區域中的虛擬機器 DNS 記錄，並無法由客戶查看或編輯。 您可以將自動註冊的 DNS 記錄覆寫為於區域中手動建立的 DNS 記錄。 下列問題和解答會說明這個議題。

### <a name="what-happens-when-we-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>當我嘗試在具有和註冊虛擬網路中自動註冊的現有虛擬機器相同之主機名稱的私人區域中手動建立新的 DNS 記錄時，會發生什麼事？

您可以嘗試在具有和註冊虛擬網路中現有自動註冊虛擬機器相同主機名稱的私人區域中手動建立新的 DNS 記錄。 當您這樣做時，新的 DNS 記錄會覆寫自動註冊的虛擬機器記錄。 如果您再次嘗試從區域刪除此手動建立的 DNS 記錄，刪除作業會成功。 只要虛擬機器仍然存在且有附加私人 IP，就會再次自動註冊。 DNS 記錄會在區域中自動重新建立。

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>當我取消註冊虛擬網路和私人區域之間的連結時，會發生什麼事？ 來自虛擬網路的自動註冊虛擬機器記錄是否也會從區域中移除？

是。 若要解除註冊虛擬網路及私人區域之間的連結，您可以更新 DNS 區域以移除相關聯的註冊虛擬網路。 此程序也會從區域中移除自動註冊的虛擬機器記錄。 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>當我刪除已連結至私人區域的註冊或解析虛擬網路時，會發生什麼事？ 我是否需要手動更新私人區域，以取消作為註冊或解析虛擬網路的虛擬網路與區域之間的連結？

是。 當您在沒有先解除註冊或解析虛擬網路與私人區域間連結的情況下便刪除該虛擬網路時，刪除作業會成功。 但系統將不會自動解除虛擬網路與任何私人區域之間的連結。 您必須手動解除虛擬網路與私人區域之間的連結。 基於這個原因，在刪除虛擬網路之前，請先解除它和您私人區域之間的連結。

### <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>使用預設 FQDN (internal.cloudapp.net) 的 DNS 解析仍然可以運作即使在私人區域 (例如 private.contoso.com) 連結到虛擬網路嗎？

是。 私人區域並不會取代使用由 Azure 所提供之 internal.cloudapp.net 區域的預設 DNS 解析。 其會作為額外的性能或增強功能供您使用。 無論是仰賴由 Azure 所提供的 internal.cloudapp.net 或您自己的私人區域，都請使用您想要解析之目標區域的 FQDN。 

### <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>已連結虛擬網路內之虛擬機器上的 DNS 尾碼，是否會變更成私人區域的尾碼？

沒有。 您已連結虛擬網路中虛擬機器上的 DNS 尾碼會保持為由 Azure 所提供的預設尾碼 ("*.internal.cloudapp.net")。 您可以手動將虛擬機器上的 DNS 尾碼變更為私人區域的尾碼。 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>此預覽期間的私人區域功能是否有任何限制？

是。 在公開預覽其間，將會存在下列限制。
* 每個私人區域只能有一個註冊虛擬網路。
* 每個私人區域最多可以有 10 個解析虛擬網路。
* 特定虛擬網路只能連結到單一私人區域作為註冊虛擬網路。
* 特定虛擬網路可以連結到最多 10 個私人區域作為解析虛擬網路。
* 如果指定了註冊虛擬網路，在已註冊至私人區域的該虛擬網路中，將無法從 PowerShell、CLI 或 API 檢視或擷取來自該虛擬網路 VM 的 DNS 記錄。 VM 記錄已註冊，且解析會成功。
* 反向 DNS 只適用於註冊虛擬網路中的私人 IP 空間。
* 未註冊到私人區域的私人 IP 反向 DNS 會傳回 "internal.cloudapp.net" 作為 DNS 尾碼。 此尾碼無法解析。 例如，作為解析虛擬網路連結到私人區域的虛擬網路中，其中所含虛擬機器的私人 IP。
* 第一次將虛擬網路連結至作為註冊或解析虛擬網路的私人區域時，虛擬網路必須是空白的。 對於未來要作為註冊或解析虛擬網路連結到其他私人區域的虛擬網路，則可為非空白。
* 不支援條件式轉送，例如，啟用 Azure 和內部部署網路之間的解析。 請了解客戶可如何透過其他機制實現此案例。 請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>針對私人區域的區域或記錄，是否有任何配額或限制？

針對私人區域，並沒有針對每個訂用帳戶所允許的區域數目限制。 針對私人區域，並沒有針對每個區域的記錄集數目限制。 公用與私人區域都會計入整體的 DNS 限制。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制](../azure-subscription-service-limits.md#azure-dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>私人區域是否有入口網站支援？

透過 API、PowerShell、CLI 和 SDK 所建立的私人區域都會顯示於 Azure 入口網站上。 但客戶無法建立新的私人區域或管理虛擬網路之間的關聯性。 對於關聯為註冊虛擬網路的虛擬網路，自動註冊的 VM 記錄不會顯示於入口網站上。 

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure DNS](dns-overview.md)。

- [深入了解如何將 Azure DNS 用於私人網域](private-dns-overview.md)。

- [深入了解 DNS 區域和記錄](dns-zones-records.md)。

- [開始使用 Azure DNS](dns-getstarted-portal.md)。
