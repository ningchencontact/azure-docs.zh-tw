---
title: "在 Azure Site Recovery 中建立及自訂用於容錯移轉和復原的復原方案 | Microsoft Docs"
description: "描述如何建立及自訂 Azure Site Recovery 中的復原方案，以容錯移轉和復原 VM 與實體伺服器"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/26/2018
ms.author: raynew
ms.openlocfilehash: 9839a989246b28c1a194b8d1f0e99c1bd80ac2e5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="create-recovery-plans"></a>建立復原方案


本文提供在 [Azure Site Recovery](site-recovery-overview.md) 中建立和自訂復原方案的相關資訊。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

 建立復原方案可執行下列作業：

* 定義會一起容錯移轉並啟動的機器群組。
* 將機器分到同一個復原計畫群組，藉以建立機器間的相依性。 例如，若要執行容錯移轉，並帶出特定的應用程式，應將該應用程式的所有 VM 分到相同的復原計畫群組。
* 執行容錯移轉。 您可以透過復原方案執行測試，以及執行規劃或未規劃的容錯移轉。

## <a name="why-use-recovery-plans"></a>為何要使用復原方案？

復原方案可藉由建立可管理的小型獨立單位，協助您針對系統性復原程序做規劃。 這些單位通常代表您環境中的應用程式。 復原方案不僅可讓您定義虛擬機器的啟動順序，也可協助您將復原期間的一般工作自動化。


**基本上，其中一個可確認您已為雲端移轉或災害復原做好準備的方法，就是確保將您的每個應用程式都加入到復原方案中，並且每個復原方案都已通過復原至 Microsoft Azure 的測試。有了這樣的準備狀態，您就能信心十足地將整個資料中心移轉或容錯移轉至 Microsoft Azure。**
 
以下是復原方案的三個重要價值主張：

### <a name="model-an-application-to-capture-dependencies"></a>建立應用程式模型以擷取相依性

復原方案是一組通常包含應用程式且一起容錯移轉的虛擬機器。 藉由使用復原方案建構，您可以增強這個群組來擷取應用程式特定屬性。
 
讓我們以一個具有下列項目的典型三層應用程式為例：

* 一個 SQL 後端
* 一個中介軟體
* 一個 Web 前端

復原方案可經由自訂來確保虛擬機器在容錯移轉後以正確的順序啟動。 SQL 後端應該最先啟動，中介軟體應該第二個啟動，Web 前端應該最後啟動。 這個順序可確保應用程式在最後一部虛擬機器啟動時已在運作。 例如，當中介軟體啟動時，它會嘗試連線到 SQL 層，而復原方案已確保 SQL 層已經處於執行中。 前端伺服器最後啟動也可確保在所有元件都已啟動並執行，且應用程式已準備好接受要求之前，使用者不會誤連到應用程式 URL。 若要建置這些相依性，您可以自訂復原方案來新增群組。 然後選取虛擬機器並變更其群組，以將它在群組之間移動。

![範例復原方案](./media/site-recovery-create-recovery-plans/rp.png)

完成自訂之後，您可以將確切的復原步驟以視覺化方式呈現。 以下是復原方案的容錯移轉期間所執行的步驟順序：

* 首先，會有一個嘗試將內部部署虛擬機器關閉的關機步驟 (在測試容錯移轉中除外，因為主要網站必須繼續執行)
* 接著，它會以平行方式觸發復原方案中所有虛擬機器的容錯移轉。 容錯移轉步驟會從複寫的資料中準備虛擬機器磁碟。
* 最後，啟動群組會以其順序執行，啟動每個群組中的虛擬機器 - 群組 1 最先，群組 2 其次，群組 3 最後。 如果有任何群組中有多部虛擬機器 (例如負載平衡的 Web 前端)，這些虛擬機器將會以平行方式啟動。

**跨群組排序可確保遵守各種應用程式層之間的相依性，而適用的平行處理原則則可改善應用程式復原的 RTO。**

   > [!NOTE]
   > 單一群組中的機器會以平行方式容錯移轉。 不同群組中的機器會依群組順序容錯移轉。 只有在群組 1 的所有機器都已容錯移轉並啟動之後，群組 2 的機器才會開始其容錯移轉。

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>將大多數復原工作自動化以縮短 RTO

復原大型應用程式可能是相當複雜的工作。 此外，記住容錯移轉或移轉後的確切自訂步驟也很困難。 有時，不是您，而是其他未注意到應用程式複雜性的人需要觸發容錯移轉。 在混亂的情況下要記得太多手動步驟，不僅困難，也容易出錯。 復原方案可讓您使用 Microsoft Azure 自動化 Runbook，將需要在每個步驟採取的必要動作自動化。 使用 Runbook 時，您可以將一般復原工作自動化，如以下範例所示。 針對那些無法自動化的工作，復原方案也提供讓您插入手動動作的功能。

* 容錯移轉後 Azure 虛擬機器上的工作 – 這些通常是必要的工作，以便讓您能夠連線至虛擬機器，例如：
    * 在容錯移轉後於虛擬機器上建立公用 IP
    * 將 NSG 指派給已容錯移轉之虛擬機器的 NIC
    * 將負載平衡器新增至可用性設定組
* 容錯移轉後虛擬機器內的工作 – 這些工作會重新設定應用程式，以便讓應用程式在新環境中正確運作，例如：
    * 修改虛擬機器內的資料庫連接字串
    * 變更 Web 伺服器設定/規則

**有了使用自動化 Runbook 將復原後工作自動化的完整復原方案，您只需一鍵即可完成容錯移轉並將 RTO 最佳化。**

### <a name="test-failover-to-be-ready-for-a-disaster"></a>測試容錯移轉以做好災害因應準備

復原方案既可用來觸發容錯移轉，也可觸發測試容錯移轉。 在進行容錯移轉之前，您應該一律先在應用程式上完成測試容錯移轉。 測試容錯移轉可協助您檢查應用程式是否會在復原網站上啟動。  如果您遺漏某個項目，您可以輕鬆觸發清除，然後重做測試容錯移轉。 請執行測試容錯移轉多次，直到您確定應用程式可順暢復原為止。

![測試復原方案](./media/site-recovery-create-recovery-plans/rptest.png)

**每個應用程式各有不同，因此您必須建置為每個應用程式自訂的復原方案。此外，在這個動態資料中心的世界裡，應用程式及其相依性都在不斷改變。請一季為您的應用程式進行一次測試容錯移轉，以確認復原方案是最新的。**

## <a name="how-to-create-a-recovery-plan"></a>如何建立復原方案

1. 按一下 [復原計畫] > [建立復原計畫]。
   指定復原方案的名稱，以及來源和目標伺服器。 來源位置必須有已啟用容錯移轉和復原功能的虛擬機器。 請根據要參與復原方案的虛擬機器，選擇來源和目標。 

   |案例                   |來源               |目標           |
   |---------------------------|---------------------|-----------------|
   |Azure 至 Azure             |Azure 區域         |Azure 區域     |
   |VMware 至 Azure            |組態伺服器 |Azure            |
   |VMM 至 Azure               |VMM 易記名稱    |Azure            |
   |Hyper-V 網站至 Azure      |Hyper-V 網站名稱    |Azure            |
   |實體機器至 Azure |組態伺服器 |Azure            |
   |VMM 至 VMM                 |VMM 易記名稱    |VMM 易記名稱|

   > [!NOTE]
   > 復原方案可以包含具有相同來源和目標的虛擬機器。 VMware 和 VMM 的虛擬機器不可以屬於相同復原方案。 不過，VMware 虛擬機器和實體機器則可加入到相同的方案中作為來源，因為兩者都是設定伺服器。

2. 在 [選取虛擬機器] 中，選取您要在復原計畫中新增至預設群組 (Group 1) 的虛擬機器 (或複寫群組)。 可選取的虛擬機器僅限在「來源」上受到保護 (如復原放案中所選取) 且對「目標」進行保護 (如復原放案中所選取) 的虛擬機器。

## <a name="how-to-customize-and-extend-recovery-plans"></a>如何自訂和擴充復原方案

您可以到 Site Recovery 復原方案資源刀鋒視窗並按一下 [自訂] 索引標籤，來自訂和擴充復原方案。

您可以自訂和擴充復原計畫：

- **新增群組**—將額外的復原計畫群組 (最多七個) 新增至預設群組，然後將更多電腦或複寫群組新增至這些復原計畫群組。 群組會依您加入的順序加以編號。 虛擬機器或複寫群組只能包含在一個復原計畫群組中。
- 新增手動動作 — 您可以新增手動動作，在建立復原方案群組之前或之後執行。 執行復原計畫時，它會在您插入手動動作之處停止。 對話方塊會提示您指定手動動作已完成。
- 新增指令碼 — 您可以新增指令碼，在建立復原計畫群組之前或之後執行。 新增指令碼後，群組中會加入新的動作集。 例如，系統會為 Group 1 建立一組前置步驟，並取名為 Group 1: Pre-steps。 所有前置步驟都會列在此動作集內。 若您已部署 VMM 伺服器，則只能在主要網站上新增指令碼。 [深入了解](site-recovery-how-to-add-vmmscript.md)。
- **新增 Azure Runbook**— 您可以使用 Azure runbook 擴充復原計畫。 例如，自動化工作或建立單步驟復原。 [深入了解](site-recovery-runbook-automation.md)。


## <a name="how-to-add-a-script-runbook-or-manual-action-to-a-plan"></a>如何將指令碼、Runbook 或手動動作新增至方案

您可以在將 VM 或複寫群組新增至預設的復原方案群組並建立方案之後，將指令碼或手動動作新增至預設復原方案群組。

1. 開啟復原計畫。
2. 按一下 [步驟] 清單中的任意項目，然後按一下 [指令碼] 或 [手動動作]。
3. 指定要在已選取項目之前或之後新增指令碼或動作。 若要將指令碼的位置向上或向下移動，請使用 [上移] 和 [下移] 按鈕。
4. 如果您新增 VMM 指令碼，請選取 [容錯移轉至 VMM 指令碼]。 在**指令碼路徑**中，輸入要共用的相對路徑。 在下列 VMM 範例中，您指定路徑︰**\RPScripts\RPScript.PS1**。
5. 如果新增 Azure 自動化 Runbook，請指定 Runbook 所在的 [Azure 自動化帳戶]，並選取適當的 [Azure Runbook 指令碼]。
6. 若要確認指令碼會如預期般運作，請執行復原方案容錯移轉。

指令碼或 Runbook 選項僅限在下列案例中執行容錯移轉或容錯回復時，才可供使用。 手動動作同時適用於容錯移轉和容錯回復。


|案例               |容錯移轉 |容錯回復 |
|-----------------------|---------|---------|
|Azure 至 Azure         |Runbook |Runbook  |
|VMware 至 Azure        |Runbook |NA       | 
|VMM 至 Azure           |Runbook |指令碼   |
|Hyper-V 網站至 Azure  |Runbook |NA       |
|VMM 至 VMM             |指令碼   |指令碼   |


## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-failover.md) 如何執行容錯移轉。

請觀看此影片來了解復原方案的實際運作情況。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
