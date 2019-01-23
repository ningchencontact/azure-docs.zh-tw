---
title: SQL Server 可用性群組 - Azure 虛擬機器 - 概觀 | Microsoft Docs
description: 本文介紹 Azure 虛擬機器上的「SQL Server 可用性群組」。
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: 5f8ae6d9138a7413b0cca4cca7bcc47c13212674
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358046"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL Server Always On 可用性群組簡介 #

本文介紹 Azure 虛擬機器上的 SQL Server 可用性群組。 

「Azure 虛擬機器」上的 Always On 可用性群組與內部部署的 Always On 可用性群組類似。 如需詳細資訊，請參閱 [Always On 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。 

下圖顯示「Azure 虛擬機器」中完整「SQL Server 可用性群組」的各個部分。

![可用性群組](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

「Azure 虛擬機器」中「可用性群組」的主要差異在於 Azure 虛擬機器需要[負載平衡器](../../../load-balancer/load-balancer-overview.md)。 負載平衡器會保有可用性群組接聽程式的 IP 位址。 如果您有多個可用性群組，則每個群組都需要一個接聽程式。 一個負載平衡器可以支援多個接聽程式。

此外，在 Azure IaaS VM 客體容錯移轉叢集上，我們建議使用每個伺服器 (叢集節點) 的單一 NIC 和單一子網路。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要額外的 NIC 和子網路。 雖然叢集驗證報告會發出節點只能在單一網路上連線的警告，但您可以放心地在 Azure IaaS VM 客體容錯移轉叢集上忽略此警告。 

當您已準備好在「Azure 虛擬機器」上建置 SQL Server 可用性群組時，請參考這些教學課程。

## <a name="automatically-create-an-availability-group-from-a-template"></a>從範本自動建立可用性群組

[在 Azure VM 中自動設定 Always On 可用性群組 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>在 Azure 入口網站中手動建立可用性群組

您也可以自行建立虛擬機器，而不使用範本。 首先，請完成必要條件，然後再建立可用性群組。 請參閱下列主題： 

- [設定 Azure 虛擬機器上 SQL Server Always On 可用性群組的必要條件](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [建立 Always On 可用性群組以改善可用性和災害復原](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>後續步驟

[在不同區域中的 Azure 虛擬機器上設定 SQL Server Always On 可用性群組](virtual-machines-windows-portal-sql-availability-group-dr.md)。
