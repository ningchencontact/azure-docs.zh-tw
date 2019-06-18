---
title: Azure 私人 DNS 常見問題集
description: 關於 Azure 私人 DNS 的常見問題集
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: c963cb1b6930b41a703b479e0213311d971e6606
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082842"
---
# <a name="azure-private-dns-faq"></a>Azure 私人 DNS 常見問題集

> [!IMPORTANT]
> Azure 私人 DNS 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支援私人網域？

使用 Azure 私人 DNS 區域功能支援用於私人網域的支援。 私人 DNS 區域會與網際網路面向 Azure DNS 區域中使用的相同工具來管理。 但只能從您指定的虛擬網路內將它們進行解析。 如需詳細資訊，請參閱[概觀](private-dns-overview.md)。

如需有關 Azure 中其他內部 DNS 選項的資訊，請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS 私人區域是否能跨 Azure 區域運作？

是。 私人區域支援在跨 Azure 區域的虛擬網路之間進行 DNS 解析。 就算是沒有明確對虛擬網路進行對等，私人區域也能運作。 所有虛擬網路都必須針對私人區域指定為解析虛擬網路。 客戶可能需要對虛擬網路進行對等，來使 TCP/HTTP 流量能夠從某個區域流至另一個區域。

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>針對私人區域，虛擬網路是否需要能夠連線至網際網路？

沒有。 私人區域能與虛擬網路搭配運作。 客戶可以使用私人區域在虛擬網路內及虛擬網路之間管理虛擬機器或其他資源的網域。 名稱解析並不需要任何網際網路連線能力。

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>相同的私人區域是否可以用於數個虛擬網路以進行解析？

是。 您可以將最多 1000年虛擬網路與單一私人區域。

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>可以屬於不同的訂用帳戶的虛擬網路可新增為連結的虛擬網路至私人區域？

是。 您必須在虛擬網路及私人 DNS 區域上皆擁有寫入作業權限。 寫入權限可授與給數個 RBAC 角色。 例如，傳統網路參與者 RBAC 角色便具有虛擬網路的寫入權限。 如需 RBAC 角色的詳細資訊，請參閱[角色型存取控制](../role-based-access-control/overview.md)。

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>將私人區域中的自動註冊的虛擬機器 DNS 記錄會自動刪除時刪除虛擬機器嗎？

是。 如果您刪除連結的虛擬網路內的虛擬機器與啟用的自動註冊程序時，會自動刪除註冊的記錄。

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>可以從連結的虛擬網路的私人區域中的自動註冊的虛擬機器記錄手動刪除？

是。 您可以將自動註冊的 DNS 記錄覆寫為於區域中手動建立的 DNS 記錄。 下列問題和解答會說明這個議題。

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>當我嘗試以手動方式建立連結的虛擬網路中具有相同的主機名稱，自動註冊的現有虛擬機器的私人區域中的 新的 DNS 記錄時，會發生什麼事？

您嘗試以手動方式建立連結的虛擬網路中具有相同的主機名稱，作為現有的自動註冊虛擬機器的私人區域中的 新的 DNS 記錄。 當您這樣做時，新的 DNS 記錄會覆寫自動註冊的虛擬機器記錄。 如果您再次嘗試從區域刪除此手動建立的 DNS 記錄，刪除作業會成功。 只要虛擬機器仍然存在且有附加私人 IP，就會再次自動註冊。 DNS 記錄會在區域中自動重新建立。

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>當我們取消連結已連結虛擬網路和私人區域時，會發生什麼事？ 來自虛擬網路的自動註冊虛擬機器記錄是否也會從區域中移除？

是。 若要取消連結的虛擬網路和私人區域的連結，您可以更新 DNS 區域，以移除相關聯的虛擬網路連結。 此程序也會從區域中移除自動註冊的虛擬機器記錄。

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>當我們刪除連結的虛擬網路連結至私人區域時，會發生什麼事？ 我們必須手動更新私人區域，以取消連結作為連結的虛擬網路，從區域的虛擬網路嗎？

是。 當您刪除連結的虛擬網路，而不先取消其連結從私人區域時，您的刪除作業就會成功。 但系統將不會自動解除虛擬網路與任何私人區域之間的連結。 您必須手動解除虛擬網路與私人區域之間的連結。 基於這個原因，在刪除虛擬網路之前，請先解除它和您私人區域之間的連結。

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>使用預設 FQDN (internal.cloudapp.net) 的 DNS 解析仍然可以運作即使在私人區域 (例如 private.contoso.com) 連結到虛擬網路嗎？

是。 私人區域並不會取代使用由 Azure 所提供之 internal.cloudapp.net 區域的預設 DNS 解析。 其會作為額外的性能或增強功能供您使用。 無論是仰賴由 Azure 所提供的 internal.cloudapp.net 或您自己的私人區域，都請使用您想要解析之目標區域的 FQDN。

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>已連結虛擬網路內之虛擬機器上的 DNS 尾碼，是否會變更成私人區域的尾碼？

沒有。 您已連結虛擬網路中虛擬機器上的 DNS 尾碼會保持為由 Azure 所提供的預設尾碼 ("*.internal.cloudapp.net")。 您可以手動將虛擬機器上的 DNS 尾碼變更為私人區域的尾碼。

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Azure 私人 DNS 的使用方式限制有哪些？

當您使用 Azure 私人 DNS 時，就會適用下列的預設限制。

| 資源 | 預設限制 |
| --- | --- |
|每個訂閱的私人 DNS 區域|1000|
|每個私人 DNS 區域的記錄集|25,000|
|每一記錄集的記錄|20|
|每個私人 DNS 區域的虛擬網路連結|1000|
|啟用自動註冊的私人 DNS 區域每一虛擬網路連結|100|
|虛擬網路可以取得連結到已啟用自動註冊的私人 DNS 區域數量|1|
|您可以取得連結的虛擬網路的私人 DNS 區域的數目|1000|

## <a name="is-there-portal-support-for-private-zones"></a>私人區域是否有入口網站支援？

[是]，並透過 Api、 PowerShell、 CLI 和 Sdk 已建立的私人區域會顯示在 Azure 入口網站。

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure 私人 DNS](private-dns-overview.md)