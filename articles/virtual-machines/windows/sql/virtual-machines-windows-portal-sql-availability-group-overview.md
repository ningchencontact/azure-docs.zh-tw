---
title: SQL Server 可用性群組 - Azure 虛擬機器 - 概觀 | Microsoft Docs
description: 本文介紹 Azure 虛擬機器上的「SQL Server 可用性群組」。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
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
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325797"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SQL Server Always On 可用性群組簡介 #

本文介紹 Azure 虛擬機器上的 SQL Server 可用性群組。 

「Azure 虛擬機器」上的 Always On 可用性群組與內部部署的 Always On 可用性群組類似。 如需詳細資訊，請參閱 [Always On 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。 

下圖顯示「Azure 虛擬機器」中完整「SQL Server 可用性群組」的各個部分。

![可用性群組](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

「Azure 虛擬機器」中「可用性群組」的主要差異在於 Azure 虛擬機器需要[負載平衡器](../../../load-balancer/load-balancer-overview.md)。 負載平衡器會保有可用性群組接聽程式的 IP 位址。 如果您有多個可用性群組，則每個群組都需要一個接聽程式。 一個負載平衡器可以支援多個接聽程式。

此外，在 Azure IaaS VM 客體容錯移轉叢集上，我們建議每個伺服器 （叢集節點） 的單一 NIC 和單一子網路。 Azure 網路有實體備援，因此 Azure IaaS VM 客體叢集上不需要額外的 NIC 和子網路。 雖然叢集驗證報告會發出節點只能在單一網路上連線的警告，但您可以放心地在 Azure IaaS VM 客體容錯移轉叢集上忽略此警告。 

|  | Windows Server 版本 | SQL Server 版本 | SQL Server 版本 | WSFC 仲裁組態 | 使用多個區域的 DR | 多重子網路支援 | 支援現有的 AD | 使用多區域相同區域的 DR | Dist AG 支援，並沒有 AD 網域 | Dist AG 支援與任何叢集 |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | 雲端見證 | 否 | yes | 是 | 是 | 否 | 否 |
| [快速入門範本](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | 雲端見證 | 否 | yes | 是 | 是 | 否 | 否 |
| [入口網站範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | 檔案共用 | 否 | 否 | 否 | 否 | 否 | 否 |
| [手動](virtual-machines-windows-portal-sql-availability-group-prereq.md) | 全部 | 全部 | 全部 | 全部 | 是 | 是 | 是 | 是 | 是 | 是 |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

當您已準備好在「Azure 虛擬機器」上建置 SQL Server 可用性群組時，請參考這些教學課程。

## <a name="manually-with-azure-cli"></a>以手動方式使用 Azure CLI
使用 Azure CLI 來設定和部署可用性群組是部署的建議的選項，因為它是部署的以簡潔且高速的最佳。 使用 Azure CLI，建立 Windows 容錯移轉叢集，SQL Server Vm 加入叢集，以及建立接聽程式和內部負載平衡器可以全部達成在 30 分鐘內。 此選項仍需要手動建立可用性群組，但會自動執行的其他必要的設定步驟。 

如需詳細資訊，請參閱 <<c0> [ 使用 Azure SQL VM CLI 設定 Azure VM 上的 SQL Server Always On 可用性群組](virtual-machines-windows-sql-availability-group-cli.md)。 

## <a name="automatically-with-azure-quickstart-templates"></a>自動使用 Azure 快速入門範本
Azure 快速入門範本會利用 SQL VM 資源的部署 Windows 容錯移轉叢集、 SQL Server Vm 加入、 建立接聽程式，並設定內部負載平衡器提供者。 此選項，仍然需要手動建立可用性群組和內部負載平衡器 (ILB)，但自動化並簡化所有其他必要的設定步驟 （包括的 ILB 設定）。 

如需詳細資訊，請參閱 <<c0> [ 使用 Azure 快速入門範本，若要設定 Azure VM 上的 SQL Server Always On 可用性群組](virtual-machines-windows-sql-availability-group-quickstart-template.md)。


## <a name="automatically-with-an-azure-portal-template"></a>自動使用 Azure 入口網站範本

[在 Azure VM 中自動設定 Always On 可用性群組 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>在 Azure 入口網站中手動操作

您也可以自行建立虛擬機器，而不使用範本。 首先，請完成必要條件，然後再建立可用性群組。 請參閱下列主題： 

- [設定 Azure 虛擬機器上 SQL Server Always On 可用性群組的必要條件](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [建立 Always On 可用性群組以改善可用性和災害復原](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>後續步驟

[在不同區域中的 Azure 虛擬機器上設定 SQL Server Always On 可用性群組](virtual-machines-windows-portal-sql-availability-group-dr.md)
