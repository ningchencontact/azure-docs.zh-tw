---
title: Azure SQL Database 受控執行個體決定Vnet/子網路大小 | Microsoft Docs
description: 本主題會講解如何計算要部署 Azure SQL Database 受控執行個體的子網路大小。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: WenJason
ms.author: v-jay
ms.reviewer: bonova, carlrab
manager: digimobile
origin.date: 12/12/2018
ms.date: 03/11/2019
ms.openlocfilehash: 2a10876bc3c9558de29caf9fee2ae0b06ee87f28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405336"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>決定 Azure SQL Database 受控執行個體的 VNet 子網路大小

Azure SQL Database 受控執行個體必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 內。

可在 VNet 子網路中部署的受控執行個體數目，取決於子網路的大小 (子網路範圍)。

當您建立受控執行個體時，Azure 會依據您在佈建期間選取的層來配置虛擬機器數目。 因為這些虛擬機器與您的子網路相關聯，所以它們需要 IP 位址。 為了確保正常作業和服務維護期間的高可用性，Azure 可能會配置額外的虛擬機器。 因此，子網路中所需的 IP 位址數目會大於該子網路中的受控執行個體數目。

根據設計，受控執行個體在子網路中需要最少 16 個 IP 位址，可能會使用多達 256 個 IP 位址。 因此，在定义子网 IP 范围时，可以使用介于 /28 和 /24 之间的子网掩码。 对于单一的常规用途或业务关键部署，网络掩码位 /28（每个网络 14 台主机）是一个不错的大小。 对于位于同一 VNet 内的多个托管实例部署，掩码位 /27（每个网络 30 台主机）是理想的。 掩码位设置 /26（62 台主机）和 /24（254 台主机）允许进一步横向扩展 VNet 来支持更多的托管实例。

> [!IMPORTANT]
> 具有 16 个 IP 地址的子网大小是绝对最小值，进一步实现托管实例横向扩展的潜力有限。強烈建議您選擇前置詞為 /27 或以下的子網路。

## <a name="determine-subnet-size"></a>決定子網路大小

如果您打算在子網路內部署多個受控執行個體，且需要將子網路大小最佳化，請使用這些參數來進行計算：

- Azure 會在子網路中針對自己的需求使用 5 個 IP 位址
- 每個一般用途執行個體都需要 2 個位址
- 每個業務關鍵執行個體都需要四個位址

**範例**：您計劃要有三個一般用途和兩個業務關鍵受控執行個體。 這表示您需要 5 + 3 * 2 + 2 * 4 = 19 個 IP 位址。 因為 IP 範圍是以 2 的乘冪定義，所以您需要 32 (2^5) 個 IP 位址的 IP 範圍。 因此，您需要保留子網路遮罩為 /27 的子網路。

> [!IMPORTANT]
> 上方顯示的計算會變成過時並需要進一步改善。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md) (英文)。
- 深入了解[受控執行個體的連線架構](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[建立將在其中部署受控執行個體的 VNet](sql-database-managed-instance-create-vnet-subnet.md) (英文)
- 針對 DNS 問題，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)
