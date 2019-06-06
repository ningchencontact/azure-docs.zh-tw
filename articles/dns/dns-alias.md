---
title: Azure DNS 別名記錄概觀
description: Microsoft Azure DNS 中對別名記錄的支援概觀。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 5/13/2019
ms.author: victorh
ms.openlocfilehash: b34baa6f1ba91935fc6307dbb1617393786043b9
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692843"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 別名記錄概觀

DNS 記錄集必須要有 Azure DNS 別名記錄。 它們可以參考您的 DNS 區域內的其他 Azure 資源。 例如，您可以建立一個別名記錄集，該別名記錄集會參考 Azure 公用 IP 位址，而不參考 A 記錄。 您的別名記錄集會動態指向 Azure 公用 IP 位址服務執行個體。 因此，別名記錄集可以在 DNS 解析期間順暢地自行更新。

Azure DNS 區域中的下列記錄類型支援別名記錄集： 

- A
- AAAA
- CNAME

> [!NOTE]
> 如果您想要使用 A 或 AAAA 記錄類型的別名記錄以指向 [Azure 流量管理員設定檔](../traffic-manager/quickstart-create-traffic-manager-profile.md)，您必須確保流量管理員設定檔只有[外部端點](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)。 您必須針對流量管理員中的外部端點，提供 IPv4 或 IPv6 位址。 您無法在端點中使用完整網域名稱 (Fqdn)。 在理想情況下，請使用靜態 IP 位址。

## <a name="capabilities"></a>功能

- **從 DNS A/AAAA 記錄集指向公用 IP 資源**。 您可以建立 A/AAAA 記錄集，並使其成為別名記錄集來指向公用 IP 資源。 如果公用 IP 位址變更或已刪除，DNS 記錄集會是自動的。 系統會避免使用指向不正確 IP 位址的懸空 DNS 記錄。

- **從 DNS A/AAAA/CNAME 記錄集指向流量管理員設定檔。** 您可以建立 A/AAAA 或 CNAME 記錄設定，並使用別名記錄將其指向流量管理員設定檔。 當您因為區域頂點不支援傳統的 CNAME 記錄，而需要在區域頂點路由傳送流量時，這點格外有用。 例如，假設您的流量管理員設定檔為 myprofile.trafficmanager.net，而您公司的 DNS 區域為 contoso.com。 您可以針對 contoso.com (區域頂點) 建立 A/AAAA 類型的別名記錄集，並指向 myprofile.trafficmanager.net。
- **指向 Azure 內容傳遞網路 (CDN) 端點**。 當您建立使用 Azure 儲存體和 Azure CDN 的靜態網站時，這非常有用。
- **指向相同區域中的另一個 DNS 記錄集**。 別名記錄可以參考其他相同類型的記錄集。 例如，DNS CNAME 記錄集可以是另一個 CNAME 記錄集所用的別名。 如果您想要讓某些記錄集成為別名，某些成為非別名，便可使用這種安排。

## <a name="scenarios"></a>案例

以下提供別名記錄的一些常見案例。

### <a name="prevent-dangling-dns-records"></a>防止 DNS 記錄懸空

傳統 DNS 記錄的常見問題是懸空記錄。 例如，DNS 記錄尚未更新以反映 IP 位址變更。 這類問題會特別發生於 A/AAAA 或 CNAME 記錄類型。

使用傳統的 DNS 區域記錄，如果目標 IP 或 CNAME 不再存在，與其相關聯的 DNS 記錄必須手動更新。 在某些組織中，手動更新可能因為流程問題，或者因為將角色與相關聯的權限層級分隔開來而不會即時發生。 比方說，角色可能有權限刪除屬於應用程式的 CNAME 或 IP 位址。 但它並沒有足夠的權限可更新指向這些目標的 DNS 記錄。 DNS 記錄更新延遲可能會導致使用者中斷。

別名記錄會將 DNS 記錄的生命週期與 Azure 資源緊密結合，以避免懸空參考。 例如，限定為別名記錄以指向公用 IP 位址或流量管理員設定檔的 DNS 記錄。 如果這些基礎資源遭到刪除，則 DNS 別名記錄會同時遭到移除。

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>應用程式 IP 位址變更時會自動更新 DNS 記錄集

這個案例類似於前一個案例。 或許是應用程式已移動，或是基礎虛擬機器已重新啟動。 當基礎公用 IP 資源的 IP 位址變更時，別名記錄則會自動更新。 這樣能避免將已指派的舊公用 IP 位址，導向使用者至其他應用程式的潛在安全性風險。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>位於區域頂點的主機負載平衡應用程式

DNS 通訊協定可防止在區域頂點指派 CNAME 記錄。 例如，如果您的網域為 contoso.com；您可以建立 somelable.contoso.com 的 CNAME 記錄；但您無法為 contoso.com 本身建立 CNAME。
此限制會為在 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)後面有負載平衡應用程式的應用程式擁有者帶來問題。 使用流量管理員設定檔需要建立 CNAME 記錄，因此不可能從區域頂點指向流量管理員設定檔。

此問題可以使用別名記錄來解決。 不同於 CNAME 記錄，別名記錄可以在區域頂點建立，而且應用程式擁有者可以使用它，將區域頂點記錄指向具有外部端點的流量管理員設定檔。 應用程式擁有者可以指向相同的流量管理員設定檔，以用於其 DNS 區域內的任何其他網域。

例如，contoso.com 和 www\.contoso.com 可以指向相同的流量管理員設定檔。 若要深入了解使用 Azure 流量管理員設定檔的別名記錄，請參閱「後續步驟」一節。

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>指向的 Azure CDN 端點的區域頂點

就像一個流量管理員設定檔中，您也可以使用別名記錄的 Azure CDN 端點指向您的 DNS 區域頂點。 當您建立使用 Azure 儲存體和 Azure CDN 的靜態網站時，這非常有用。 然後，您就可以不含前面加上"www"您的 DNS 名稱來存取網站。

比方說，如果您的靜態網站名稱為 www.contoso.com，您的使用者可以存取您的網站使用 contoso.com，而不需要在前面加上 www 的 DNS 名稱。

如先前所述，在區域頂點不支援 CNAME 記錄。 因此，您無法使用的 CNAME 記錄以指向您的 CDN 端點的 contoso.com。 相反地，您可以使用別名記錄，以直接指向 CDN 端點的區域頂點。

> [!NOTE]
> 目前不支援來自 Akamai 的 Azure CDN 指向區域頂點至 CDN 端點。

## <a name="next-steps"></a>後續步驟

若要深入了解別名記錄，請參閱下列文章：

- [教學課程：設定別名記錄以參照 Azure 公用 IP 位址](tutorial-alias-pip.md)
- [教學課程：使用流量管理員設定別名記錄以支援頂點網域名稱](tutorial-alias-tm.md)
- [DNS 常見問題](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
