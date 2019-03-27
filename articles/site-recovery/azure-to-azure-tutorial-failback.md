---
title: 使用 Azure Site Recovery 服務，容錯回復已複寫到次要 Azure 區域的 Azure IaaS VM，以進行災害復原。
description: 了解如何使用 Azure Site Recovery 服務容錯回復 Azure VM。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: d8721f313907f0e0519dca52f5565853f1c44110
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089690"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>在 Azure 區域之間容錯回復 Azure VM

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何容錯回復單一 Azure VM。 在容錯移轉之後，您可以容錯回復到可用的主要區域。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> 
> * 容錯回復次要 VM
> * 重新保護回復到次要區域的主要 VM
> 
> [!NOTE]
> 
> 本教學課程旨在透過最少的自訂來逐步引導使用者對目標區域進行容錯移轉及容錯回復；如果您想要深入了解容錯移轉的各個相關層面，包括網路功能考量、自動化或疑難排解，請參閱 Azure VM 的「操作說明」文件。

## <a name="prerequisites"></a>必要條件

> * 確定 VM 處於 [容錯移轉已認可] 狀態，並檢查主要區域可供使用，而且您可以建立及存取其中的新資源。
> * 確定已啟用重新保護。

## <a name="fail-back-to-the-primary-region"></a>容錯回復至主要區域

重新保護 VM 之後，您可以視需要容錯回復到主要區域。

1. 移至您的復原服務保存庫。 按一下 [複寫的項目]，然後選取已重新受到保護的 VM。

2. 您應該會看見下列內容。 請注意，其類似於用來從主要區域中測試容錯移轉和容錯移轉的刀鋒視窗。
![容錯回復到主要區域](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. 按一下 [測試容錯移轉] 以執行要回到主要區域的容錯移轉測試。 基於測試容錯移轉選擇復原點和虛擬網路，然後選取 [確定]。 您會看見在可存取並檢查之主要區域中建立的測試 VM。

4. 當您滿意測試容錯移轉之後，可以按一下 [清除測試容錯移轉] 來清除測試容錯移轉之來源區域中建立的資源。

5. 在 [複寫的項目] 中，選取您要容錯移轉的 VM > [容錯移轉]。

6. 在 [容錯移轉] 中，選取容錯移轉的目標 [復原點]。 您可以使用下列其中一個選項：
    1. 最新 (預設值)：此選項可在 Site Recovery 服務中處理所有資料，並提供最低的復原點目標 (RPO)
    2. 最新處理：此選項會將虛擬機器還原到 Site Recovery 服務已處理的最新復原點
    3. 自訂：使用此選項來容錯移轉到特定復原點。 此選項適合用於執行測試容錯移轉

7. 如果想要在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源虛擬機器關機，請選取 [先將機器關機再開始容錯移轉]。 即使關機失敗，仍會繼續容錯移轉。 請注意，Site Recovery 並不會在容錯移轉後清除來源。

8. 在 [作業] 頁面上追蹤容錯移轉進度

9. 容錯移轉之後，登入虛擬機器進行驗證。 如果您想要前往虛擬機器的另一個復原點，則可使用 [變更復原點] 選項。

10. 一旦您滿意容錯移轉的虛擬機器之後，就可以認可容錯移轉。 認可會刪除服務可用的所有復原點。 [變更復原點] 選項已無法再使用。

![主要和次要區域上的 VM](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

如果您看到上述螢幕擷取畫面，則表示 "ContosoWin2016" VM 已從美國中部容錯移轉至美國東部，並從美國東部容錯回復至美國中部。

請注意，DR VM 將停留在關機已解除配置的狀態。 這是故意設計的行為，因為 Azure Site Recovery 會儲存虛擬機器的資訊，這可能會對之後從主要區域到次要區域的容錯移轉很有用。 已解除配置的虛擬機器不會收費，因此請讓其保持原狀。

> [!NOTE]
> 如需重新保護工作流程和重新保護期間會發生什麼情況的詳細資訊，請參閱[「操作說明」一節](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) \(機器翻譯\)。
