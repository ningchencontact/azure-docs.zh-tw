---
title: 什麼是 Azure 可用性區域？ | Microsoft Docs
description: 若要在 Azure 中建立高度可用且具有恢復功能的應用程式，可用性區域可提供可讓您用來執行資源的實體不同位置。
services: ''
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: iainfou
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 9eb7105b2d1a95eb8ccfa96ea0bc5188aab1b4aa
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164716"
---
# <a name="what-are-availability-zones-in-azure"></a>什麼是 Azure 中的可用性區域？
「可用性區域」是高可用性供應項目，可保護您的應用程式和資料不受資料中心故障影響。 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 地區內「可用性區域」的實體區隔可保護應用程式和資料不受資料中心故障影響。 區域備援服務會將應用程式和資料複寫至所有「可用性區域」，以防止發生單一失敗點情況。 使用「可用性區域」時，Azure 可提供業界最佳的 99.99% VM 執行時間 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 說明保證的 Azure 整體可用性。

藉由將運算、儲存體、網路及資料資源共置於某個區域內並複寫至其他區域，即可讓您的應用程式架構內建高可用性。 支援「可用性區域」的 Azure 服務分成兩個類別：

- **區域服務** – 您需將資源釘選至特定區域 (例如虛擬機器、受控磁碟、IP 位址) 或
- **區域備援服務** – 平台會自動跨區域複寫 (例如區域備援儲存體、SQL Database)。

若要在 Azure 上達到全面性的商務持續性，請使用「可用性區域」與 Azure 地區配對的組合來建置您的應用程式架構。 您可以使用 Azure 地區內的「可用性區域」來同步複寫應用程式和資料以提供高可用性，並以非同步方式跨 Azure 地區複寫以提供災害復原保護。
 
![地區中細分至一個區域的概念性檢視](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>支援可用性區域的地區

- 美國中部
- 法國中部
- 美國東部 2 (預覽)
- 西歐 (預覽)
- 東南亞 (預覽)


## <a name="services-that-support-availability-zones"></a>支援可用性區域的服務
支援可用性區域的 Azure 服務：

- Linux 虛擬機器
- Windows 虛擬機器
- 虛擬機器擴展集
- 受控磁碟
- Load Balancer
- 公用 IP 位址
- 區域備援儲存體
- SQL Database


## <a name="pricing"></a>價格
針對部署在「可用性區域」中的虛擬機器並不會產生額外費用。 將兩部以上的虛擬機器部署在 Azure 地區內兩個以上的「可用性區域」中時，即可提供 99.99% VM 執行時間 SLA。 將會有額外的「可用性區域」間 VM 對 VM 資料傳輸費用。 如需詳細資訊，請檢閱[頻寬定價](https://azure.microsoft.com/pricing/details/bandwidth/)頁面。


## <a name="get-started-with-availability-zones"></a>開始使用可用性區域
- [建立虛擬機器](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 來新增受控磁碟](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [建立區域備援虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [區域備援儲存體](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)


## <a name="next-steps"></a>後續步驟
- [快速入門範本](http://aka.ms/azqs)
