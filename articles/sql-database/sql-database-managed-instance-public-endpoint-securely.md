---
title: 保護受管理的執行個體的公用端點-Azure SQL Database 受控執行個體 |Microsoft Docs
description: 安全地在 Azure 中使用的公用端點，與受管理的執行個體
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470289"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>安全地使用 Azure SQL Database 受控執行個體，具有公用端點

Azure SQL Database 受控執行個體可以透過提供使用者連接管道[的公用端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 這篇文章說明如何進行此設定更安全。

## <a name="scenarios"></a>案例

SQL Database 受控執行個體提供私用的端點，以允許從其虛擬網路內的連線。 預設選項是提供最大的隔離。 不過，有您要提供公用端點連線的案例：

- 受管理的執行個體必須與多-租用戶專用平台為-即服務 (PaaS) 供應項目整合。
- 您需要更高的輸送量，比使用 VPN 時，資料交換。
- 公司原則禁止公司網路內的 PaaS。

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>部署受管理的執行個體的公用端點存取

雖然並非必要，常見的部署模型，具有公用端點存取的 managed 執行個體是隔離的專用虛擬網路中建立執行個體。 在此組態中，虛擬網路僅用於虛擬叢集隔離。 如果受管理的執行個體的 IP 位址空間與公司網路的 IP 位址空間重疊，並不重要。

## <a name="secure-data-in-motion"></a>移動中的安全資料

如果用戶端驅動程式支援加密，一律會加密受管理的執行個體資料流量。 受管理的執行個體和其他 Azure 虛擬機器或 Azure 服務之間傳送的資料永遠不會離開 Azure 的骨幹。 如果沒有受管理的執行個體與內部部署網路之間的連線，我們建議您就會使用 Azure ExpressRoute Microsoft 對等互連。 ExpressRoute 可協助您避免透過公用網際網路移動資料。 受控執行個體的私人連線能力，可以使用只有私用對等互連。

## <a name="lock-down-inbound-and-outbound-connectivity"></a>鎖住輸入和輸出連線

下圖顯示建議的安全性設定：

![安全性設定之鎖定的輸入和輸出連線](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

受管理的執行個體都有[專用的公用端點位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。 在用戶端輸出防火牆和網路安全性群組規則，此公用端點 IP 位址設定來限制輸出連線能力。

若要確保受管理的執行個體的流量來自受信任的來源，建議您從已知的 IP 位址的來源連接。 使用網路安全性群組來限制存取的受管理的執行個體的公用端點連接埠 3342 上。

當用戶端需要起始從內部部署網路的連線時，請確定原始位址就會轉譯成一組已知的 IP 位址。 如果您不能這麼做 （例如，行動工作力所的典型案例），我們建議您使用[點對站 VPN 連線和私用端點](sql-database-managed-instance-configure-p2s.md)。

如果連接從 Azure 開始，我們建議流量，來自已知指派[虛擬 IP 位址](../virtual-network/virtual-networks-reserved-public-ip.md)（比方說，虛擬機器）。 若要讓管理更容易的虛擬 IP (VIP) 位址，您可能想要使用[公用 IP 位址首碼](../virtual-network/public-ip-address-prefix.md)。

## <a name="next-steps"></a>後續步驟

- 了解如何設定管理的執行個體的公用端點：[設定公用端點](sql-database-managed-instance-public-endpoint-configure.md)
