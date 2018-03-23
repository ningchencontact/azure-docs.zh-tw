---
title: 跨 Azure 區域的 SAP HANA 可用性 | Microsoft Docs
description: 說明在 Azure VM 上執行 SPA HANA 時的可用性考量概觀
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>跨 Azure 區域的 SAP HANA 可用性
本文會說明及討論跨不同 Azure 區域的 SAP HANA 可用性相關案例。 假設個別 Azure 區域之間的距離較遠，在本文中會列出特殊考量。

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>跨多個 Azure 區域部署的動機
不同 Azure 區域是依據較遠的距離分隔。 相依於地緣政治區域，距離可能是數百英哩甚至數千英哩，像是在美國境內。 由於不同 Azure 區域之間的距離，部署於兩個不同 Azure 區域之資產間的流量，會經歷明顯的網路往返延遲。 明顯到足以排除典型 SAP 工作負載底下兩個 SAP HANA 執行個體之間的同步資料交換。 另一方面，您通常會面臨一個事實，就是您的主要資料中心與次要資料中心之間的距離有已定義的需求，以便在天然災害襲擊更廣泛區域時提供可用性。 例如在 2017 年 9 月和 10 月侵襲加勒比海和佛羅里達州地需的颶風。 或至少有最小距離需求。 在大部分客戶案例中，此最小距離定義需要由您針對跨 [Azure 區域](https://azure.microsoft.com/regions/)的可用性進行設計。 因為兩個 Azure 區域之間的距離太遠，而無法使用 HANA 的同步複寫模式，RTO 和 RPO 的需求可能會強制您在一個區域內部署可用性組態，然後在第二個區域中補充額外部署。

在這些組態中要考慮的另一個層面就是容錯移轉和用戶端重新導向。 在此假設兩個不同 Azure 區域中 SAP HANA 執行個體之間的容錯移轉一律為手動容錯移轉。 因為 SAP HANA 系統複寫的複寫模式設定為非同步，所以有可能在主要 HANA 執行個體中認可的資料尚未認可到次要 HANA 執行個體。 因此複寫為非同步的組態無法接受自動容錯移轉。 即使使用手動控制的容錯移轉，如同在容錯移轉練習中，您都需要採取相關措施，以確定在主要端上的所有認可資料，在手動移至其他 Azure 區域之前認可至次要執行個體。
 
因為您在 Azure VNet 中不同的 IP 位址範圍中操作 (它們會部署在第二個 Azure 區域中)，所以 SAP HANA 用戶端需要在其組態中變更，或者更好的方法是您需要備妥步驟以變更名稱解析。 因此，用戶端會重新導向至新的次要伺服器 IP 位址。 有關[接管後用戶端連線復原](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html)的 SAP 文章會更詳細說明。   

## <a name="simple-availability-between-two-azure-regions"></a>兩個 Azure 區域之間的簡單可用性
在此案例中，您決定不要在單一區域內設定任何可用性。 但是，您可以在發生災害時要求提供工作負載。 這類系統的典型案例為非生產系統。 雖然您可以承受系統關閉半天或甚至一天，但是不允許系統無法使用 48 小時 (含) 以上。 為了讓設定的成本更低，您可在作為目的地的 VM 上執行另一個較不重要的系統，或者將次要區域中的 VM 大小調小，並選擇不要預先載入資料。 由於即將手動容錯移轉，而且需要更多步驟才能容錯移轉完整應用程式堆疊，所以關閉 VM、調整其大小及再次啟動 VM 的額外時間是可接受的。

> [!NOTE]
> 即使 HANA 系統複寫目標中沒有預先載入的資料，您都需要至少 64GB 記憶體以上，才能在目標執行個體的記憶體中保存資料列存放區資料。

![兩個區域有兩部 VM](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> 在此組態中，您無法提供 RPO=0，因為您的 HANA 系統複寫模式是非同步。 如果您需要提供 RPO=0，此組態不是適合選擇的組態。

設定資料預先載入是可在組態上進行的小變動。 不過由於手動容錯移轉的本質和應用程式層級也必須移至第二個區域的事實，預先載入資料可能沒有意義。 

## <a name="combine-availability-within-one-region-and-across-regions"></a>結合一個區域內和跨區域的可用性 
下列項目可促進區域內和跨區域可用性的結合：

- Azure 區域內 RPO=0 的需求。
- 不願意或無法讓公司的全域作業受到會影響較大區域的大型天然災難影響。 過去幾年侵襲加勒比海的幾個颶風造成這種情況。
- 要求主要與次要網站之間的距離明顯超過 Azure 可用性區域可提供之距離的規定。

 
在這些情況下，您要設定哪個 SAP 會使用 HANA 系統複寫呼叫 [SAP HANA 多層式系統複寫組態](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html)。 架構如下所示：

![兩個區域有三部 VM](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

這個組態會在主要區域內提供 RPO=0 與很小的 RTO 時間，並且在移至第二個區域的情況下額外提供子系 RPO。 第二個區域中的 RTO 時間取決於您是否設定資料預先載入。 在許多客戶案例中，次要區域中的 VM 是用來執行測試系統。 因為這個使用方式，所以無法預先載入資料。

> [!NOTE]
> 因為您針對從第 1 層移至第 2 層的 HANA 系統複寫 (主要區域中的同步複寫) 使用 logreplay 作業模式，所以第 2 層和第 3 層之間的複寫 (複寫到次要網站) 不能是 delta_datashipping 作業模式。 SAP 會將作業模式和一些限制的詳細資料記錄在 [SAP HANA 系統複寫的作業模式](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)中。 

## <a name="next-steps"></a>後續步驟
如果您需要如何在 Azure 中設定這類組態的逐步指引，請參閱下列文章：

- [在 Azure 虛擬機器中設定 SAP HANA 系統複寫](sap-hana-high-availability.md)
- [Azure 上的 SAP – 第 4 部分 – 使用系統複寫的 SAP HANA 高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
