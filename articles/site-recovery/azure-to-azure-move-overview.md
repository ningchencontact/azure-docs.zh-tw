---
title: 使用 Azure Site Recovery 服務將 Azure IaaS VM 移到其他 Azure 區域 | Microsoft Docs
description: 使用 Azure Site Recovery，將 Azure IaaS VM 從一個 Azure 區域移至另一個區域。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5d844692b6199d93fa835da1021c9753311e17de
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824512"
---
# <a name="move-azure-vms-to-another-region"></a>將 Azure VM 移動到另一個區域

Azure 隨著客戶群的增長而持續擴充，且將新增新區域的支援以因應不斷上升的需求。 此外，每個月也都會新增跨服務的新功能。 因此，您有時候會想要將 VM 移至不同的區域或可用性區域中，以提高可用性。

本文將逐步說明您會想要移轉 VM 的各種案例，並提供如何在目標中設定架構以提升可用性的指南。 
> [!div class="checklist"]
> * [為何要移動 Azure VM](#why-would-you-move-azure-vms)
> * [如何移動 Azure VM](#how-to-move-azure-vms)
> * [一般架構](#typical-architectures-for-a-multi-tier-deployment)
> * [將 VM 依原狀移至目標區域](#move-azure-vms-to-another-region)
> * [移動 VM 以提高可用性](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>為何要移動 Azure VM

客戶移動 VM 的可能原因如下：

- 如果您已在某個區域中部署，但其後 Azure 新增了新區域的支援，且該區域較接近您應用程式或服務的使用者，此時您就會想要將 **VM 依原狀移至新區域**，以降低延遲。 如果您想要合併訂用帳戶，或是有控管/組織規則要求您移動 VM，也可以採用相同的方法。 
- 如果您的 VM 部署為單一執行個體 VM，或部署為可用性設定組的一部分，而您想要提高可用性 SLA，您可以**將 VM 移至可用性設定組**。 

## <a name="how-to-move-azure-vms"></a>如何移動 Azure VM
要移動 VM 必須執行下列步驟：

1. 驗證必要條件 
2. 準備來源 VM 
3. 準備目標區域 
4. 將資料複製到目標區域 - 使用 Azure Site Recovery 複寫技術，將資料從來源 VM 複製到目標區域
5. 測試組態：複寫完成後，對非生產網路執行測試容錯移轉，藉以測試組態。
6. 執行移動 
7. 捨棄來源區域中的資源 


> [!IMPORTANT]
> 目前 Azure Site Recovery 支援在不同的區域間移動 VM，但不支援在區域內部移動。 

> [!NOTE]
> 此處說明的各種案例，都可在文件中找到前述步驟的詳細指引

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>多層式部署的一般架構
下一節將逐步說明客戶針對 Azure 中的多層式應用程式最常採用的部署架構。 此處的範例說明的是使用公用 IP 的三層式應用程式。 每一層 (Web、應用程式和資料庫) 各有 2 個 VM，並且由負載平衡器連線到其他層。 資料庫層的 VM 之間會進行 SQL Always ON 複寫，以達到高可用性 (HA)。

1.  **單一執行個體 VM 部署於不同層** - 一層中的每個 VM 都會設定為單一執行個體 VM，並且由負載平衡器連線到其他層。 在客戶採用的組態中，這是最簡單的一種。

       ![single-VMs](media/move-vm-overview/regular-deployment.PNG)

2. **每層中的 VM 部署在可用性設定組間** - 一層中的每個 VM 都設定於可用性設定組中。 [可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)可確保您在 Azure 上部署的 VM 會分散到叢集中多個各自獨立的硬體節點。 這麼做可以確保當 Azure 發生硬體或軟體故障時，受到影響的只會是一部分的 VM 子集，您整體的解決方案則會維持可用且正常運作。 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **每層中的 VM 部署在可用性設定組間** - 一層中的每個 VM 設定於[可用性區域](https://docs.microsoft.com/azure/availability-zones/az-overview)間。 Azure 區域中的可用性區域是由容錯網域和更新網域組成。 例如，如果您在 Azure 區域中建立橫跨三個區域的三個 (或更多) VM，您的 VM 會有效地分散到三個容錯網域和三個更新網域。 Azure 平台會從更新網域中辨識此分佈，以確定不會同時更新不同區域中的 VM。

      ![zone-deploymnt](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>將 VM 依原狀移至目標區域

根據前述的[架構](#typical-architectures-for-a-multi-tier-deployment)，以下將說明您執行依原狀移至目標區域的作業後，部署所將呈現的樣貌。


1. **單一執行個體 VM 部署於不同層** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **每層中的 VM 部署在可用性設定組間**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **每層中的 VM 部署在可用性區域間**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>移動 VM 以提高可用性

1. **單一執行個體 VM 部署於不同層** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **每層中的 VM 部署在可用性設定組間** - 選擇使用 Azure Site Recovery 為 VM 啟用複寫時，您可以選擇設定為將可用性設定組中的 VM 放在個別的可用性區域中。 完成移動作業後，可用性 SLA 將達到 99.9 %。

      ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>後續步驟

在本文件中，您已了解移動 VM 的一般指引。 若想了解其逐步執行方式，請進一步參閱：


> [!div class="nextstepaction"]
> * [將 Azure VM 移至其他區域](azure-to-azure-tutorial-migrate.md)

> * [將 Azure VM 移至可用性區域中](move-azure-VMs-AVset-Azone.md)

