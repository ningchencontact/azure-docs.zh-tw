---
title: 管理和監視 Azure VM 備份使用 Azure 備份服務
description: 了解如何管理和監視 Azure VM 備份使用 Azure 備份服務。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: aa953440f03137f3359276bc9e06cb0c73f0ab4a
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295771"
---
# <a name="manage-azure-vm-backups"></a>管理 Azure VM 備份

這篇文章說明如何管理 Azure 的虛擬機器 (Vm) 使用備份[Azure 備份服務](backup-overview.md)。 本文件也摘要了保存庫儀表板上的備份資訊可以找到。


在 Azure 入口網站中，復原服務保存庫儀表板會提供存取保存庫的詳細資訊，包括：

* 最新的備份，也就是最新的還原點。
* 備份原則。
* 所有備份快照的大小總計。
* 已啟用的備份的 Vm 數目。

藉由使用儀表板和向下的個別 Vm 鑽研，您可以管理備份。 若要開始機器備份，請開啟保存庫儀表板上。

![使用滑桿的完整儀表板檢視](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>檢視儀表板上的 Vm

若要檢視保存庫儀表板上的 Vm:

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表中，選取**瀏覽**。 在資源清單中輸入 **復原服務**。 當您輸入時，清單會篩選根據您的輸入。 選取 [復原服務保存庫]。

    ![建立復原服務保存庫](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 為了方便使用，以滑鼠右鍵按一下該保存庫，然後選取**釘選到儀表板**。
4. 開啟保存庫儀表板。

    ![開啟 [設定] 刀鋒視窗與保存庫儀表板](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. 在 **備份項目**磚中，選取**Azure 虛擬機器**。

    ![開啟 [備份項目] 圖格](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. 在  **備份項目** 刀鋒視窗中，您可以檢視受保護的 Vm 清單。 在此範例中，保存庫能保護一部虛擬機器： demobackup。  

    ![檢視 [備份項目] 刀鋒視窗](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. 從保存庫項目的儀表板中，修改備份原則、 執行隨選備份、 停止或繼續 Vm 的保護、 刪除備份資料、 檢視還原點，和執行還原。

    ![備份項目儀表板和 [設定] 刀鋒視窗](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>管理 VM 的備份原則

若要管理的備份原則：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 開啟保存庫儀表板。
2. 在 **備份項目**磚中，選取**Azure 虛擬機器**。

    ![開啟 [備份項目] 圖格](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. 在  **備份項目** 刀鋒視窗中，您可以檢視受保護的 Vm 和最新的還原點時間與上次備份狀態的清單。

    ![檢視 [備份項目] 刀鋒視窗](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. 從保存庫項目的儀表板，您可以選取備份原則。

   * 若要切換原則，選取不同的原則，然後選取**儲存**。 新原則時會立即套用至保存庫。

     ![選擇備份原則](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>執行隨選備份
設定其保護之後，您可以執行 VM 的隨選備份。 請記住這些詳細資料：

- 如果初始備份已暫止，隨選備份會建立 VM 的完整複本在復原服務保存庫中。
- 如果初始備份已完成，視備份會只將變更傳送上一個快照集復原服務保存庫。 也就是更新備份一律是增量。
- 隨選備份的保留範圍是您指定當您觸發備份的保留值。

若要觸發隨選備份：

1. 在上[保存庫項目儀表板](#view-vms-on-the-dashboard)下方**保護的項目**，選取**備份項目**。

    ![備份現在選項](./media/backup-azure-manage-vms/backup-now-button.png)

2. 從**備份管理類型**，選取**Azure 虛擬機器**。 **備份項目 （Azure 虛擬機器）** 刀鋒視窗隨即出現。
3. 選取 VM，然後選取**立即備份**建立隨選備份。 **立即備份**刀鋒視窗隨即出現。
4. 在 **保留備份到**欄位中，指定要保留備份的日期。

    ![立即備份行事曆](./media/backup-azure-manage-vms/backup-now-check.png)

5. 選取 **確定**執行備份作業。

若要追蹤作業的進度，請在保存庫儀表板中，選取**備份作業**圖格。

## <a name="stop-protecting-a-vm"></a>停止保護 VM

有兩種方式可停止保護 VM:

- 停止所有未來的備份作業並刪除所有復原點。 在此情況下，您將無法還原 VM。
- 停止所有未來的備份作業，並保留復原點。 雖然您會需要保存庫中保留復原點，用多少付多少，您將能夠還原 VM，如有需要。 如需詳細資訊，請參閱 < [Azure 備份定價](https://azure.microsoft.com/pricing/details/backup/)。

>[!NOTE]
>如果您刪除資料來源而不需停止備份時，新的備份將會失敗。 根據原則，將到期的舊的復原點，但將一律會保留最後一個復原點，直到您停止備份並刪除資料。
>

若要停止 VM 的保護：

1. 在 [保存庫項目的儀表板](#view-vms-on-the-dashboard)，選取**停止備份**。
2. 選擇是否要保留或刪除備份資料，並視需要請確認您的選取項目。 如果您想要請新增註解。 如果您不確定項目的名稱，請暫留在驚嘆號來檢視的名稱。

    ![停止保護](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     通知可讓您知道備份作業已停止。

## <a name="resume-protection-of-a-vm"></a>繼續保護 VM

如果當您停止 VM 時，您就會保留備份資料，您可以稍後繼續保護。 如果您刪除備份資料時，您無法繼續保護。

若要繼續保護 VM:

1. 在 [保存庫項目的儀表板](#view-vms-on-the-dashboard)，選取**繼續備份**。

2. 請依照下列中的步驟[管理備份原則](#manage-backup-policy-for-a-vm)vm 指派的原則。 您不需要選擇 VM 的初始的保護原則。
3. 您套用至 VM 的備份原則之後，您會看到下列訊息：

    ![訊息，指出已成功保護的 VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>刪除備份資料

您可以刪除虛擬機器的備份資料期間**停止備份**作業或在備份作業完成之後。 刪除備份資料之前，請記住這些詳細資料：

- 它可能是個不錯的主意，等候幾天或數週之前刪除復原點。
- 不同於處理程序來還原復原點，當您刪除備份資料，您無法選擇刪除特定的復原點。 如果您刪除備份資料時，您就會刪除所有相關聯的復原點。

在您停止或停用 VM 的備份作業之後，您可以刪除備份資料：


1. 在 [保存庫項目儀表板](#view-vms-on-the-dashboard)，選取**刪除備份資料**。

    ![選取 刪除備份](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. 輸入備份項目，以確認您想要刪除復原點的名稱。

    ![確認您想要刪除復原點](./media/backup-azure-manage-vms/item-verification-box.png)

1. 若要刪除之項目的備份資料，請選取**刪除**。 通知訊息可讓您知道已刪除備份資料。

## <a name="next-steps"></a>後續步驟
- 了解如何[從 VM 的 設定 Azure Vm 備份](backup-azure-vms-first-look-arm.md)。
- 了解如何[還原 Vm](backup-azure-arm-restore-vms.md)。
- 了解如何[監視 Azure VM 備份](backup-azure-monitor-vms.md)。
