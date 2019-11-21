---
title: Alias records overview - Azure DNS
description: In this article, learn about support for alias records in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: allensu
ms.openlocfilehash: da396d3e3db4acd1a9843977b8b3e3d51c33f021
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212331"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 別名記錄概觀

DNS 記錄集必須要有 Azure DNS 別名記錄。 它們可以參考您的 DNS 區域內的其他 Azure 資源。 例如，您可以建立一個別名記錄集，該別名記錄集會參考 Azure 公用 IP 位址，而不參考 A 記錄。 您的別名記錄集會動態指向 Azure 公用 IP 位址服務執行個體。 因此，別名記錄集可以在 DNS 解析期間順暢地自行更新。

Azure DNS 區域中的下列記錄類型支援別名記錄集： 

- A
- AAAA
- CNAME

> [!NOTE]
> 如果您想要使用 A 或 AAAA 記錄類型的別名記錄以指向 [Azure 流量管理員設定檔](../traffic-manager/quickstart-create-traffic-manager-profile.md)，您必須確保流量管理員設定檔只有[外部端點](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)。 您必須針對流量管理員中的外部端點，提供 IPv4 或 IPv6 位址。 You can't use fully-qualified domain names (FQDNs) in endpoints. 在理想情況下，請使用靜態 IP 位址。

## <a name="capabilities"></a>容量

- **從 DNS A/AAAA 記錄集指向公用 IP 資源**。 You can create an A/AAAA record set and make it an alias record set to point to a public IP resource (standard or basic). The DNS record set changes automatically if the public IP address changes or is deleted. 系統會避免使用指向不正確 IP 位址的懸空 DNS 記錄。

   There is a current limit of 20 alias records sets per resource.

- **從 DNS A/AAAA/CNAME 記錄集指向流量管理員設定檔。** 您可以建立 A/AAAA 或 CNAME 記錄設定，並使用別名記錄將其指向流量管理員設定檔。 It's especially useful when you need to route traffic at a zone apex, as traditional CNAME records aren't supported for a zone apex. 例如，假設您的流量管理員設定檔為 myprofile.trafficmanager.net，而您公司的 DNS 區域為 contoso.com。 您可以針對 contoso.com (區域頂點) 建立 A/AAAA 類型的別名記錄集，並指向 myprofile.trafficmanager.net。
- **Point to an Azure Content Delivery Network (CDN) endpoint**. This is useful when you create static websites using Azure storage and Azure CDN.
- **指向相同區域中的另一個 DNS 記錄集**。 別名記錄可以參考其他相同類型的記錄集。 例如，DNS CNAME 記錄集可以是另一個 CNAME 記錄集所用的別名。 如果您想要讓某些記錄集成為別名，某些成為非別名，便可使用這種安排。

## <a name="scenarios"></a>案例

以下提供別名記錄的一些常見案例。

### <a name="prevent-dangling-dns-records"></a>防止 DNS 記錄懸空

傳統 DNS 記錄的常見問題是懸空記錄。 For example, DNS records that haven't been updated to reflect changes to IP addresses. 這類問題會特別發生於 A/AAAA 或 CNAME 記錄類型。

使用傳統的 DNS 區域記錄，如果目標 IP 或 CNAME 不再存在，與其相關聯的 DNS 記錄必須手動更新。 In some organizations, a manual update might not happen in time because of process issues or the separation of roles and associated permission levels. 比方說，角色可能有權限刪除屬於應用程式的 CNAME 或 IP 位址。 但它並沒有足夠的權限可更新指向這些目標的 DNS 記錄。 DNS 記錄更新延遲可能會導致使用者中斷。

別名記錄會將 DNS 記錄的生命週期與 Azure 資源緊密結合，以避免懸空參考。 例如，限定為別名記錄以指向公用 IP 位址或流量管理員設定檔的 DNS 記錄。 If you delete those underlying resources, the DNS alias record becomes an empty record set. It no longer references the deleted resource.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>應用程式 IP 位址變更時會自動更新 DNS 記錄集

這個案例類似於前一個案例。 或許是應用程式已移動，或是基礎虛擬機器已重新啟動。 當基礎公用 IP 資源的 IP 位址變更時，別名記錄則會自動更新。 這樣能避免將已指派的舊公用 IP 位址，導向使用者至其他應用程式的潛在安全性風險。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>位於區域頂點的主機負載平衡應用程式

DNS 通訊協定可防止在區域頂點指派 CNAME 記錄。 For example if your domain is contoso.com; you can create CNAME records for somelabel.contoso.com; but you can't create CNAME for contoso.com itself.
此限制會為在 [Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)後面有負載平衡應用程式的應用程式擁有者帶來問題。 Since using a Traffic Manager profile requires creation of a CNAME record, it isn't possible to point at the Traffic Manager profile from the zone apex.

This problem is solved using alias records. Unlike CNAME records, alias records are created at the zone apex and application owners can use it to point their zone apex record to a Traffic Manager profile that has external endpoints. Application owners point to the same Traffic Manager profile that's used for any other domain within their DNS zone.

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. 若要深入了解使用 Azure 流量管理員設定檔的別名記錄，請參閱「後續步驟」一節。

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Point zone apex to Azure CDN endpoints

Just like a Traffic Manager profile, you can also use alias records to point your DNS zone apex to Azure CDN endpoints. This is useful when you create static websites using Azure storage and Azure CDN. You can then access the website without prepending "www" to your DNS name.

For example, if your static website is named www.contoso.com, your users can access your site using contoso.com without the need to prepend www to the DNS name.

As described previously, CNAME records aren't supported at the zone apex. So, you can’t use a CNAME record to point contoso.com to your CDN endpoint. Instead, you can use an alias record to point the zone apex to a CDN endpoint directly.

> [!NOTE]
> Pointing a zone apex to CDN endpoints for Azure CDN from Akamai is currently not supported.

## <a name="next-steps"></a>後續步驟

若要深入了解別名記錄，請參閱下列文章：

- [教學課程：設定別名記錄來參考 Azure 公用 IP 位址](tutorial-alias-pip.md)
- [教學課程：使用流量管理員來設定別名記錄以支援頂點網域名稱](tutorial-alias-tm.md)
- [DNS 常見問題](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
