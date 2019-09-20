---
title: Azure 私人 DNS 常見問題集
description: 有關 Azure 私人 DNS 的常見問題
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: fca7359f9fa54899bb72be3b939e1a1839dbfbd1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155712"
---
# <a name="azure-private-dns-faq"></a>Azure 私人 DNS 常見問題集

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支援私人網域？

使用 Azure 私人 DNS 區域功能可支援私人網域。 私人 DNS 區域是使用與網際網路面向 Azure DNS 區域相同的工具進行管理。 但只能從您指定的虛擬網路內將它們進行解析。 如需詳細資訊，請參閱[概觀](private-dns-overview.md)。

如需有關 Azure 中其他內部 DNS 選項的資訊，請參閱 [VM 與角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS 私人區域是否能跨 Azure 區域運作？

是的。 私人區域支援在跨 Azure 區域的虛擬網路之間進行 DNS 解析。 就算是沒有明確對虛擬網路進行對等，私人區域也能運作。 所有虛擬網路都必須針對私人區域指定為解析虛擬網路。 您可能需要對等互連虛擬網路，以讓 TCP/HTTP 流量能夠從某個區域流至另一個區域。

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>針對私人區域，虛擬網路是否需要能夠連線至網際網路？

資料分割 私人區域能與虛擬網路搭配運作。 您可以使用它們來管理虛擬網路中的虛擬機器或其他資源的網域。 名稱解析並不需要任何網際網路連線能力。

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>相同的私人區域是否可以用於數個虛擬網路以進行解析？

是的。 您最多可以將1000個虛擬網路與單一私人區域建立關聯。

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>屬於不同訂用帳戶的虛擬網路是否可以新增為私人區域的連結虛擬網路？

是的。 您必須在虛擬網路及私人 DNS 區域上皆擁有寫入作業權限。 寫入權限可授與給數個 RBAC 角色。 例如，傳統網路參與者 RBAC 角色便具有虛擬網路的寫入權限。 如需 RBAC 角色的詳細資訊，請參閱[角色型存取控制](../role-based-access-control/overview.md)。

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>當您刪除虛擬機器時，會自動刪除私人區域中自動註冊的虛擬機器 DNS 記錄嗎？

是的。 如果您在已啟用自動註冊的連結虛擬網路中刪除虛擬機器，則會自動刪除已註冊的記錄。

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>是否可以手動刪除來自連結虛擬網路的私人區域中自動註冊的虛擬機器記錄？

是的。 您可以將自動註冊的 DNS 記錄覆寫為於區域中手動建立的 DNS 記錄。 下列問題和解答會說明這個議題。

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>當我嘗試手動建立新的 DNS 記錄到與連結的虛擬網路中的自動註冊現有虛擬機器具有相同主機名稱的私人區域時，會發生什麼事？

您嘗試在私人區域中手動建立新的 DNS 記錄，其主機名稱必須與連結的虛擬網路中現有、自動註冊的虛擬機器相同。 當您這樣做時，新的 DNS 記錄會覆寫自動註冊的虛擬機器記錄。 如果您再次嘗試從區域刪除此手動建立的 DNS 記錄，刪除作業會成功。 只要虛擬機器仍然存在且有附加私人 IP，就會再次自動註冊。 DNS 記錄會在區域中自動重新建立。

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>當我們將連結的虛擬網路與私人區域取消連結時，會發生什麼事？ 來自虛擬網路的自動註冊虛擬機器記錄是否也會從區域中移除？

是的。 若要將連結的虛擬網路與私人區域取消連結，請更新 DNS 區域以移除相關聯的虛擬網路連結。 此程序也會從區域中移除自動註冊的虛擬機器記錄。

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>當我們刪除連結至私人區域的連結虛擬網路時，會發生什麼事？ 我們是否必須手動更新私人區域，才能將虛擬網路從區域取消連結虛擬網路？

是的。 當您刪除連結的虛擬網路，而不先將它從私人區域取消連結時，您的刪除作業就會成功。 但系統將不會自動解除虛擬網路與任何私人區域之間的連結。 您必須手動解除虛擬網路與私人區域之間的連結。 基於這個原因，在刪除虛擬網路之前，請先解除它和您私人區域之間的連結。

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>即使私人區域（例如 private.contoso.com）已連結至虛擬網路，使用預設 FQDN （internal.cloudapp.net）的 DNS 解析仍可正常作用嗎？

是的。 私人區域並不會取代使用由 Azure 所提供之 internal.cloudapp.net 區域的預設 DNS 解析。 其會作為額外的性能或增強功能供您使用。 無論是仰賴由 Azure 所提供的 internal.cloudapp.net 或您自己的私人區域，都請使用您想要解析之目標區域的 FQDN。

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>已連結虛擬網路內之虛擬機器上的 DNS 尾碼，是否會變更成私人區域的尾碼？

資料分割 您已連結虛擬網路中虛擬機器上的 DNS 尾碼會保持為由 Azure 所提供的預設尾碼 ("*.internal.cloudapp.net")。 您可以手動將虛擬機器上的 DNS 尾碼變更為私人區域的尾碼。

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Azure 私人 DNS 的使用量限制為何？

當您使用 Azure 私人 DNS 時，將會套用下列預設限制。

| Resource | 預設限制 |
| --- | --- |
|每個訂用帳戶的私人 DNS 區域|1000|
|每個私人 DNS 區域的記錄集|25,000|
|每一記錄集的記錄|20|
|每個私人 DNS 區域的虛擬網路連結|1000|
|已啟用自動註冊的每個私人 DNS 區域的虛擬網路連結|100|
|已啟用自動註冊的虛擬網路可連結的私人 DNS 區域數目|1|
|虛擬網路可以連結的私人 DNS 區域數目|1000|

## <a name="is-there-portal-support-for-private-zones"></a>私人區域是否有入口網站支援？

是，您可以在 Azure 入口網站上看到已透過 Api、PowerShell、CLI 和 Sdk 建立的私人區域。

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>為什麼我現有的私人 DNS 區域不會顯示在新的入口網站體驗中？

在預覽重新整理版本中，我們為私人 DNS 區域推出了新的資源模型。 您現有的私人 DNS 區域必須遷移至新的資源模型，才會顯示在新的入口網站體驗中。 如需如何遷移至新資源模型的指示，請參閱下文。

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>如何? 將現有的私人 DNS 區域遷移至新的模型？
我們強烈建議您儘快遷移至新的資源模型。 將支援舊版資源模型，不過，將不會在此模型之上開發進一步的功能。 未來，我們打算以新的資源模型來取代它。 如需如何將您現有的私人 DNS 區域遷移至新資源模型的指引，請參閱[Azure DNS 私人區域的遷移指南](private-dns-migration-guide.md)。

## <a name="next-steps"></a>後續步驟

- [深入瞭解 Azure 私人 DNS](private-dns-overview.md)