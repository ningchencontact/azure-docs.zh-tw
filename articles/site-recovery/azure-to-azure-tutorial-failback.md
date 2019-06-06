---
title: 使用 Azure Site Recovery 服務，容錯回復已複寫到次要 Azure 區域的 Azure VM，以進行災害復原。
description: 了解如何使用 Azure Site Recovery 服務容錯回復 Azure VM。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a3b67e9b0dc41eeb14000400912892fbf29acfe2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399490"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>在 Azure 區域之間容錯回復 Azure VM

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何容錯回復單一 Azure VM。 在容錯移轉之後，您必須在主要區域可供使用時容錯回復到該處。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> 
> * 在次要區域中容錯回復 VM。
> * 重新保護回復到次要區域的主要 VM。
> 
> [!NOTE]
> 
> 本教學課程可協助您將一些 VM 容錯移轉到目標區域，再容錯回復到來源區域 (需要最少自訂)。 如需詳細的深入指示，請檢閱 [Azure VM 的操作指南](https://docs.microsoft.com/azure/virtual-machines/windows/)。

## <a name="before-you-start"></a>開始之前

* 確定 VM 的狀態為 [容錯移轉已認可]  。
* 確認主要區域可供使用，而且您可以建立及存取其中的新資源。
* 確定已啟用重新保護。

## <a name="fail-back-to-the-primary-region"></a>容錯回復至主要區域

重新保護 VM 之後，您可以視需要容錯回復到主要區域。

1. 在保存庫中，選取 [複寫的項目]  ，然後選取已受到重新保護的 VM。

    ![容錯回復到主要區域](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. 選取 [測試容錯移轉]  ，以執行要回到主要區域的容錯移轉測試。
4. 選取測試容錯移轉的復原點和虛擬網路，然後選取 [確定]  。 您可以檢閱在主要區域中建立的測試 VM。
5. 順利完成測試容錯移轉之後，請選取 [清除測試容錯移轉]  ，以清除在測試容錯移轉的來源區域中建立的資源。
6. 在 [複寫的項目]  中選取 VM，然後選取 [容錯移轉]  。
7. 在 [容錯移轉]  中，選取要容錯移轉的目標復原點：
    - **最新 (預設值)** ：在 Site Recovery 服務中處理所有資料，並提供最低的復原點目標 (RPO)。
    - **最近處理**：將 VM 還原到 Site Recovery 所處理的最新復原點。
    - **自訂**：容錯移轉至特定復原點。 此選項適合用於執行測試容錯移轉。

8. 如果想在觸發容錯移轉之前，讓 Site Recovery 嘗試將來源 VM 關機，請選取 [先將機器關機再開始容錯移轉]  。 即使關機失敗，仍會繼續容錯移轉。 請注意，Site Recovery 並不會在容錯移轉後清除來源。
9. 在 [作業]  頁面上追蹤容錯移轉進度。
10. 容錯移轉完成後，請登入 VM 進行驗證。 您可以視需要變更復原點。
11. 驗證容錯移轉之後，請選取 [認可容錯移轉]  。 認可作業會刪除所有可用的復原點。 無法再使用 [變更復原點] 選項。
12. VM 應會顯示為已容錯移轉且已容錯回復。

    ![主要和次要區域上的 VM](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> 災害復原 VM 將維持關機/已解除配置的狀態。 這是故意設計的，因為 Site Recovery 會儲存 VM 資訊，這可能對之後從主要區域到次要區域的容錯移轉很有用。 已解除配置的 VM 不會收費，所以應該保持原狀。

## <a name="next-steps"></a>後續步驟

[深入了解](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)重新保護流程。
