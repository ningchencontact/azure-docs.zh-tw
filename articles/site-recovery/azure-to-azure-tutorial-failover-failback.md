---
title: 使用 Azure Site Recovery 服務將複寫的 Azure IaaS VM 容錯移轉及容錯回復到次要 Azure 區域，以進行災害復原。
description: 了解如何使用 Azure Site Recovery 服務將複寫的 Azure VM 容錯移轉及容錯回復到次要 Azure 區域，以進行災害復原。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 46dae28fd6c9eaa3d5e03f5f06c5e92449653679
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737717"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>在 Azure 區域之間容錯移轉及容錯回復 Azure VM

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何將單一 Azure VM 容錯移轉到次要 Azure 區域。 在容錯移轉之後，您可以容錯回復到可用的主要區域。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 容錯移轉 Azure VM
> * 重新保護次要 Azure VM，使其能複寫到主要區域
> * 容錯回復次要 VM
> * 將主要 VM 放回次要區域保護

> [!NOTE]
> 本教學課程旨在透過最少的自訂來逐步引導使用者對目標區域進行容錯移轉及容錯回復；如果您想要深入了解容錯移轉的各個相關層面，包括網路功能考量、自動化或疑難排解，請參閱 Azure VM 的「操作說明」文件。

## <a name="prerequisites"></a>必要條件

- 確定您已完成[災害復原演練](azure-to-azure-tutorial-dr-drill.md)，檢查一切是否如預期般運作。
- 執行測試容錯移轉之前，請確認 VM 屬性。 VM 必須符合 [Azure 需求](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)。

## <a name="run-a-failover-to-the-secondary-region"></a>執行容錯移轉至次要區域

1. 在 [複寫的項目] 中，選取您要容錯移轉的 VM > [容錯移轉]。

   ![容錯移轉](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. 在 [容錯移轉] 中，選取要容錯移轉的目標**復原點**。 您可以使用下列其中一個選項：

   * **最新** (預設值)：此選項可在 Site Recovery 服務中處理所有資料，並提供最低的「復原點目標」(RPO)。
   * **最近處理**：此選項會將虛擬機器還原到 Site Recovery 服務已處理的最新復原點。
   * **自訂**：若要容錯移轉至特定復原點，請使用此選項。 此選項適合用於執行測試容錯移轉。

3. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源虛擬機器關機，請選取 [先將機器關機再開始容錯移轉]。 即使關機失敗，仍會繼續容錯移轉。 請注意，Site Recovery 並不會在容錯移轉後清除來源。

4. 在 [作業] 頁面上追蹤容錯移轉進度。

5. 容錯移轉之後，登入虛擬機器進行驗證。 如果您想要前往虛擬機器的另一個復原點，您可以使用 [變更復原點] 選項。

6. 一旦您滿意容錯移轉的虛擬機器，您可以 [認可] 容錯移轉。
   認可會刪除服務可用的所有復原點。 **變更復原點**選項已無法再使用。

## <a name="reprotect-the-secondary-vm"></a>重新保護次要 VM

在 VM 容錯移轉之後，您需要重新保護它，使其能複寫回到主要區域。

1. 確定 VM 處於 [已認可容錯移轉] 狀態，並檢查主要區域是否使用，而且您可以建立及存取其中的新資源。
2. 在 [保存庫] > [已複寫的項目] 中，以滑鼠右鍵按一下已容錯移轉的 VM，然後選取 [重新保護]。

   ![按一下滑鼠右鍵可重新保護](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. 請注意，已經選取保護方向 (次要到主要區域)。
3. 檢閱**資源群組、網路、儲存體和可用性設定組**資訊。 在重新保護作業期間，將會建立任何標記為 (新的) 的資源。
4. 按一下 [確定] 以觸發重新保護作業。 這項作業會在目標網站植入最新資料。 然後，它會將差異複寫到主要區域。 VM 目前處於受保護的狀態。

> [!NOTE]
> 如需重新保護工作流程和重新保護期間會發生什麼情況的詳細資訊，請參閱[「操作說明」一節](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)。


## <a name="fail-back-to-the-primary-region"></a>容錯回復至主要區域

重新保護 VM 之後，您可視需要容錯回復到主要區域。 若要這樣做，請設定從次要區域到主要區域的容錯移轉，如本文所述。

![按一下滑鼠右鍵可重新保護](./media/azure-to-azure-tutorial-failover-failback/failback.png)

如果您看到上述螢幕擷取畫面，則表示 "ContosoWin2016" VM 已從美國中部容錯移轉至美國東部，並從美國東部容錯回復至美國中部。

容錯移轉會關閉次要區域 (亦即災害復原區域) 中的 VM，然後在主要區域中建立 VM 並加以啟動。 請**注意** DR VM 會停留在關機的已解除配置狀態，如上所示。 這是故意設計的行為，因為 Azure Site Recovery 會儲存虛擬機器的資訊，這可能會對之後從主要區域到次要區域的容錯移轉很有用。 已解除配置的虛擬機器不會收費，因此請讓其保持原狀。
