---
title: Azure DNS 別名記錄概觀
description: Microsoft Azure DNS 中對別名記錄的支援概觀。
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092837"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS 別名記錄概觀

DNS 記錄集必須要有 Azure DNS 別名記錄。 它們可以參考您的 DNS 區域內的其他 Azure 資源。 例如，您可以建立一個別名記錄集，該別名記錄集會參考 Azure 公用 IP 位址，而不參考 A 記錄。 您的別名記錄集會動態指向 Azure 公用 IP 位址服務執行個體。 因此，別名記錄集可以在 DNS 解析期間順暢地自行更新。

Azure DNS 區域中的下列記錄類型支援別名記錄集： 

- 具有使用  
- AAAA 
- CNAME 

> [!NOTE]
> 只有外部端點類型支援適用於 Azure 流量管理員 A 或 AAAA 記錄類型的別名記錄。 您必須針對流量管理員中的外部端點，適當地提供 IPv4 或 IPv6 位址。 在理想情況下，使用靜態 IP 位址。

## <a name="capabilities"></a>功能

- **從 DNS A/AAAA 記錄集指向公用 IP 資源**。 您可以建立 A/AAAA 記錄集，並使其成為別名記錄集來指向公用 IP 資源。

- **從 DNS A/AAAA/CNAME 記錄集指向流量管理員設定檔。** 您可以從 DNS CNAME 記錄集指向流量管理員設定檔的 CNAME。 例如，contoso.trafficmanager.net。 現在，您也可以從 DNS 區域中的 A 或 AAAA 記錄集，指向具有外部端點的流量管理員設定檔。

   > [!NOTE]
   > 只有外部端點類型支援適用於流量管理員 A 或 AAAA 記錄類型的別名記錄。 您必須針對流量管理員中的外部端點，適當地提供 IPv4 或 IPv6 位址。 在理想情況下，使用靜態 IP 位址。
   
- **指向相同區域中的另一個 DNS 記錄集**。 別名記錄可以參考其他相同類型的記錄集。 例如，DNS CNAME 記錄集可以是相同類型的另一個 CNAME 記錄集所用的別名。 如果您想要讓某些記錄集成為別名，某些成為非別名，便可使用這種安排。

## <a name="scenarios"></a>案例
以下提供別名記錄的一些常見案例。

### <a name="prevent-dangling-dns-records"></a>防止 DNS 記錄懸空
 在 Azure DNS 區域中，別名記錄可用來密切追蹤 Azure 資源的生命週期。 資源包括公用 IP 或流量管理員設定檔。 傳統 DNS 記錄的常見問題是懸空記錄。 此問題會特別發生於 A/AAAA 或 CNAME 記錄類型。 

使用傳統的 DNS 區域記錄，如果目標 IP 或 CNAME 不再存在，DNS 區域記錄並不會知道此事。 因此，必須手動更新記錄。 在某些組織中，此手動更新可能不會及時發生。 這也可能會有問題，因為角色與相關聯的權限等級隔離。

比方說，角色可能有權限刪除屬於應用程式的 CNAME 或 IP 位址。 但它並沒有足夠的權限可更新指向這些目標的 DNS 記錄。 在刪除 IP 或 CNAME 與移除指向它的 DNS 記錄之間會發生時間延遲。 此時間延遲可能會導致使用者中斷。

別名記錄會移除與此案例相關聯的複雜度， 並協助避免懸空參考。 例如，限定為別名記錄以指向公用 IP 或流量管理員設定檔的 DNS 記錄。 如果這些基礎資源遭到刪除，則 DNS 別名記錄會同時遭到移除。 此流程可確保使用者永遠不會受到中斷影響。

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>當應用程式 IP 變更時自動更新 DNS 區域

這個案例類似於前一個案例。 或許是應用程式已移動，或是基礎虛擬機器已重新啟動。 當基礎公用 IP 資源的 IP 位址變更時，別名記錄則會自動更新。 若要避免潛在的安全性風險，請將使用者導向另一個具有舊 IP 位址的應用程式。

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>位於區域頂點的主機負載平衡應用程式

DNS 通訊協定可防止在區域頂點指派 A 或 AAAA 記錄以外的任何項目。 例如 contoso.com。 此限制會為在流量管理員後面有負載平衡應用程式的應用程式擁有者帶來問題。 您無法從區域頂點記錄指向流量管理員設定檔。 因此，應用程式擁有者必須使用因應措施。 位於應用程式層的重新導向，必須從區域頂點重新導向至另一個網域。 例如，從 contoso.com 重新導向至 www.contoso.com。 此種安排會為重新導向功能帶來單一失敗點。

透過別名記錄，此問題已不存在。 應用程式擁有者現在可以將其區域頂點記錄指向具有外部端點的流量管理員設定檔。 應用程式擁有者可以指向相同的流量管理員設定檔，以用於其 DNS 區域內的任何其他網域。 例如，contoso.com 和 www.contoso.com 可以指向相同的流量管理員設定檔。 只要流量管理員設定檔僅設定外部端點，就是這種情況。

## <a name="next-steps"></a>後續步驟

若要深入了解別名記錄，請參閱下列文章：

- [教學課程：設定別名記錄來參考 Azure 公用 IP 位址](tutorial-alias-pip.md)
- [教學課程：使用流量管理員來設定別名記錄以支援頂點網域名稱](tutorial-alias-tm.md)
- [DNS 常見問題](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
