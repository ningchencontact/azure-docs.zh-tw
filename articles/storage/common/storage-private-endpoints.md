---
title: 使用具有 Azure 儲存體的私用端點 |Microsoft Docs
description: 針對從虛擬網路對儲存體帳戶進行安全存取的私用端點的總覽。
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: aec12cee7466e59389b28742bf66247751a0d22b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949465"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>使用 Azure 儲存體的私用端點（預覽）

Azure 儲存體可讓虛擬網路（VNet）上的用戶端使用[私人端點](../../private-link/private-endpoint-overview.md)，透過[私人連結](../../private-link/private-link-overview.md)安全地存取儲存體帳戶中的資料。 私人端點會針對您的儲存體帳戶服務使用 VNet 位址空間中的 IP 位址。 VNet 上的用戶端與儲存體帳戶之間的網路流量會流經 VNet，並在 Microsoft 骨幹網路上進行私人連結，以消除公開網際網路的風險。

針對您的儲存體帳戶使用私人端點可讓您：
- 設定儲存體防火牆以封鎖儲存體服務的公用端點上的所有連線，藉此保護您的儲存體帳戶。
- 藉由讓您封鎖從 VNet 外泄的資料，提高虛擬網路（VNet）的安全性。
- 使用[VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)或具有私人對等互連的[ExpressRoutes](../../expressroute/expressroute-locations.md) ，安全地從連線到 VNet 的內部部署網路連線到儲存體帳戶。

## <a name="conceptual-overview"></a>概念總覽
![Azure 儲存體總覽的私用端點](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

私人端點是您的[虛擬網路](../../virtual-network/virtual-networks-overview.md)（VNet）中適用于 Azure 服務的特殊網路介面。 它會在 VNet 中的用戶端與您的儲存體帳戶之間提供安全的連線。 私人端點會從 VNet 的 IP 位址範圍指派 IP 位址。 私人端點與儲存體服務之間的連接會使用安全的私用連結。

VNet 中的應用程式可以使用相同的連接字串和授權機制，順暢地透過私人端點連接到儲存體服務，否則會使用它們。 私人端點可以與儲存體帳戶支援的所有通訊協定搭配使用，包括 REST 和 SMB。

當您在 VNet 中建立儲存體服務的私人端點時，會將同意要求傳送給儲存體帳戶擁有者。 如果要求建立私人端點的使用者也是儲存體帳戶的擁有者，則會自動核准此同意要求。

儲存體帳戶擁有者可以透過[Azure 入口網站](https://portal.azure.com)中儲存體帳戶的 [私人端點] 索引標籤，核准或拒絕同意要求，以及查看或管理私人端點。

您可以藉由設定[儲存防火牆](storage-network-security.md#change-the-default-network-access-rule)預設拒絕透過其公用端點的存取，來保護您的儲存體帳戶，使其僅接受來自 VNet 的連線。 您不需要有儲存體防火牆規則，就可以允許來自具有私人端點之 VNet 的流量，因為存放裝置防火牆規則只會套用至其公用端點。 私用端點會改為依賴同意流程來授與對儲存體服務的子網存取權。

### <a name="private-endpoints-for-storage-service"></a>儲存體服務的私人端點

建立私用端點時，您必須指定儲存體帳戶和它所連接的儲存體服務。 對於需要存取的儲存體帳戶中的每個儲存體服務（也就是[blob](../blobs/storage-blobs-overview.md)、 [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) [、檔案](../files/storage-files-introduction.md)、[佇列](../queues/storage-queues-introduction.md)、[資料表](../tables/table-storage-overview.md)或[靜態網站](../blobs/storage-blob-static-website.md)），您都需要一個私人端點。

為確保讀取[許可權異地多餘儲存體帳戶](storage-redundancy-grs.md#read-access-geo-redundant-storage)的可用性，您需要服務之主要和次要實例的個別私人端點。

#### <a name="resources"></a>資源

如需有關為您的儲存體帳戶建立私人端點的詳細資訊，請參閱下列文章：

- [從 Azure 入口網站中的儲存體帳戶體驗，私下連接至儲存體帳戶](../../private-link/create-private-endpoint-storage-portal.md)
- [在 Azure 入口網站中使用私人連結中心建立私人端點](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure CLI 建立私人端點](../../private-link/create-private-endpoint-cli.md)
- [使用 Azure PowerShell 建立私人端點](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更

當您建立儲存體服務的私人端點時，我們會將該儲存體端點的 DNS CNAME 資源記錄更新為前置詞為 '*privatelink*' 之子域中的別名。 根據預設，我們也會建立附加至 VNet 的[私人 DNS 區域](../../dns/private-dns-overview.md)。 此私人 DNS 區域會對應至前置詞為 '*privatelink*' 的子域，並包含私人端點的 DNS A 資源記錄。

當您從建立私人端點的 VNet 外部解析儲存體端點 URL 時，它仍會解析為儲存體服務的公用端點。 從裝載私用端點的 VNet 解析時，儲存體端點 URL 會解析為私人端點的 IP 位址。

針對上述範例，當從裝載私人端點的 VNet 外部解析時，儲存體帳戶 ' StorageAccountA ' 的 DNS 資源記錄將會是：

| Name                                                  | Type  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<公用端點\>                                   |
| \<公用端點\>                                   | A     | \<儲存體服務公用 IP 位址\>                 |

如先前所述，您可以使用儲存體防火牆拒絕透過公用端點的所有存取。

StorageAccountA 的 DNS 資源記錄（當由裝載私用端點的 VNet 中的用戶端解析時）會是：

| Name                                                  | Type  | Value                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

這種方法可讓您從裝載私人端點的 VNet 和 VNet 外部的用戶端，使用相同的連接字串來存取儲存體帳戶。 您可以使用儲存體防火牆來拒絕存取 VNet 以外的所有用戶端。

## <a name="pricing"></a>價格

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="known-issues"></a>已知問題

### <a name="copy-blob-failures"></a>複製 Blob 失敗

目前，當來源儲存體帳戶受到防火牆保護時，針對透過私人端點存取的儲存體帳戶所發出的[複製 Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob)命令會失敗。

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>具有私人端點的 Vnet 中用戶端的儲存體存取條件約束

在存取具有私人端點的其他儲存體帳戶時，Vnet 中具有現有儲存體私人端點的用戶端臉部條件約束。 比方說，假設 VNet N1 具有儲存體帳戶 A1 的私用端點（例如 blob 服務）。 如果儲存體帳戶 A2 在 VNet 中有適用于 blob 服務的私用端點，則 VNet N1 中的用戶端也必須使用私用端點來存取帳戶 A2 中的 blob 服務。 如果儲存體帳戶 A2 沒有 blob 服務的任何私用端點，則 VNet N1 中的用戶端可以存取其 blob 服務，而不需要私用端點。

此條件約束是當帳戶 A2 建立私人端點時，所做的 DNS 變更結果。

### <a name="nsg-rules-on-subnets-with-private-endpoints"></a>在具有私人端點的子網上 NSG 規則

目前無法針對具有私人端點的子網設定[網路安全性群組](../../virtual-network/security-overview.md)（NSG）規則。 此問題的有限因應措施是在來源子網上為私人端點執行存取規則，不過這種方法可能需要較高的管理負荷。
