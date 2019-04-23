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
ms.date: 04/16/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013472"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>使用 Azure SQL Database 受控執行個體，安全地使用公用端點

Azure SQL Database 受控執行個體無法啟用透過提供使用者的連線以便[公用端點](../virtual-network/virtual-network-service-endpoints-overview.md)。 本文提供指引來進行此設定更安全的方式。

## <a name="scenarios"></a>案例

受管理的執行個體提供私用的端點，以讓其虛擬網路內的連線。 預設選項是提供最大的隔離。 不過，有需要的公用端點連線的案例：

- 與多租用戶只有 PaaS 供應項目整合。
- 高於使用 VPN 資料交換的輸送量。
- 公司原則禁止公司網路內的 PaaS。

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>部署受管理的執行個體的公用端點存取

雖然並非必要，常見的部署模型，具有公用端點存取的 managed 執行個體是隔離的專用虛擬網路中建立執行個體。 在此組態中，虛擬網路只用於虛擬叢集隔離。 如果受管理的執行個體的 IP 位址空間與公司網路 IP 位址空間重疊，不相關。

## <a name="securing-data-in-motion"></a>在影片中保護資料

如果用戶端驅動程式支援加密，一律會加密受管理的執行個體資料流量。 受管理的執行個體與其他 Azure 虛擬機器或 Azure 服務之間的資料永遠不會離開 Azure 的骨幹。 如果沒有受管理的執行個體為內部部署網路連接，建議使用 Expressroute Microsoft 對等互連。 快速路由將有助於避免透過公用網際網路移動資料 （受控執行個體的私用網路連接，只有私人對等互連可用）。

## <a name="locking-down-inbound-and-outbound-connectivity"></a>鎖住輸入和輸出連線

下圖顯示建議的安全性設定。

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

受管理的執行個體都有[專用的公用端點位址](sql-database-managed-instance-find-management-endpoint-ip-address.md)。 此 IP 位址應該設定在用戶端端輸出防火牆和網路安全性群組規則，來限制輸出連線能力。

若要確保受管理的執行個體的流量來自受信任的來源，建議您從具有已知的 IP 位址的來源連接。 限制的存取權的受管理的執行個體的公用端點連接埠 3342 上使用網路安全性群組。

當用戶端需要起始從內部部署網路的連線時，請確定原始位址就會轉譯成一組已知的 Ip。 如果無法達到的 （例如，在一般的案例中的行動工作力），則建議使用[點對站 VPN 連線和私用端點](sql-database-managed-instance-configure-p2s.md)。

如果連接從 Azure 啟動，則建議，流量會從已知指派[VIP](../virtual-network/virtual-networks-reserved-public-ip.md) （比方說，虛擬機器）。 為了方便管理的 VIP 位址，客戶可以考慮使用[公用 IP 位址首碼](../virtual-network/public-ip-address-prefix.md)。