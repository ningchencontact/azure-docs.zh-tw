---
title: 使用 Azure 備份服務管理和監視 Azure VM 備份
description: 了解如何使用 Azure 備份服務管理和監視 Azure VM 備份。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: f5c0373e2ef094a7fc5be64f4aeb8c0bb132e683
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430058"
---
# <a name="manage-azure-vm-backups"></a>管理 Azure VM 備份

本文說明如何使用 [Azure 備份服務](backup-overview.md)管理 Azure VM 備份，並彙整入口網站儀表板中可用的備份警示資訊。


在 Azure 入口網站中，復原服務保存庫儀表板可讓您存取保存庫的相關資訊，包括︰

* 最新的備份，也就是最新的還原點。
* 備份原則
* 所有備份快照的大小總計
* 已啟用備份的 VM 數目

您可以使用儀表板管理備份，並且向下切入至個別 VM。 虛擬機器備份從儀表板中開啟保存庫開始。 

![使用滑桿以完整檢視](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>檢視儀表板中的 VM

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上按一下 [瀏覽]，然後在資源清單中輸入**復原服務**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 按一下 [復原服務保存庫] 。 
    ![建立復原服務保存庫的步驟 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. 為了方便使用，以滑鼠右鍵按一下保存庫清單中的保存庫 >[Pin to dashboard]\(釘選到儀表板\)。
3. 開啟保存庫儀表板。 
    ![開啟保存庫儀表板和設定刀鋒視窗](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. 在 [備份項目] 圖格上，按一下 [Azure 虛擬機器]。

    ![開啟備份項目備份圖格](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. 在 [備份項目] 刀鋒視窗中，您可以看到每個項目的最後一個備份作業。 在此範例中，有一個受此保存庫保護的虛擬機器：demovm markgal。  

    ![備份項目圖格](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. 從保存庫項目儀表板中，您可以建立或修改備份原則、檢視還原點、執行隨選備份、停止和繼續保護 VM、刪除復原點，以及執行還原。

    ![搭配設定刀鋒視窗的備份項目儀表板](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>管理備份原則
1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [所有設定]。

    ![備份原則刀鋒視窗](./media/backup-azure-manage-vms/all-settings-button.png)
2. 在 [設定] 中，按一下 [備份原則]。
3. 從 [選擇備份原則]  功能表︰

   * 若要變更原則，請選取不同的原則，然後按一下 [儲存] 。 新原則時會立即套用至保存庫。
   * 若要建立原則，請選取 [建立新項目] 。 [深入了解](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![虛擬機器備份](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>執行隨選備份
設定保護後，您可以執行 VM 的隨選備份。
- 如果初始備份已暫止，則隨選備份會在復原服務保存庫中建立虛擬機器的完整複本。
- 如果已完成初始備份，隨選備份只會將前一個備份快照的變更傳送到復原服務保存庫。 亦即，後續備份一律是增量備份。
- 隨選備份的保留範圍是原則中為「每日」備份點所指定的保留值。 如果未選取任何「每日」備份點，則會使用每週備份點。


若要觸發隨選備份：

1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [立即備份]。

    ![立即備份按鈕](./media/backup-azure-manage-vms/backup-now-button.png)

 2. 按一下 [是]  啟動備份作業。

    ![立即備份按鈕](./media/backup-azure-manage-vms/backup-now-check.png)

 
 備份作業會建立復原點。 復原點的保留範圍與在虛擬機器相關的原則中指定的保留範圍相同。 若要追蹤作業的進度，請在保存庫儀表板中按一下 [備份作業]  圖格。  

## <a name="stop-protecting-a-vm"></a>停止保護 VM

有兩種方式可停止保護 VM︰

- 停止所有未來的備份作業並刪除所有復原點。 您無法在此情況下還原 VM。
- 停止所有未來的備份作業但保留復原點。 在儲存體中保留復原點需要付出相關的成本。 不過，保留復原點的好處是可以在需要時還原 VM。 [深入了解](https://azure.microsoft.com/pricing/details/backup/)定價詳細資料。

若要停止保護虛擬機器：

1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [停止備份]。

    ![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button.png
2. 請視需要，選擇是否保留或刪除備份資料並確認。 請視需要確認，並選擇是否提供註解。 如果您不確定項目名稱，請以滑鼠暫留在驚嘆號來檢視名稱。

    ![停止保護](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 通知訊息可讓您知道備份作業已停止。


## <a name="resume-protection-of-a-vm"></a>繼續保護 VM

如果 VM 停止時您有保留備份資料，則可以繼續保護。 如果您刪除備份資料，則無法繼續。

Te

1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [繼續備份]。

2. 請依照[管理備份原則](backup-azure-manage-vms.md#manage-backup-policies)中的步驟，指派虛擬機器的原則。 您可以選擇與最初用於保護虛擬機器不同的原則。
3. 備份原則套用至虛擬機器後，您會看到下列訊息。

    ![已成功保護 VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>刪除備份資料

您可以在**停止備份**作業期間，或在備份作業完成之後的任何時間，刪除與 VM 相關聯的備份資料。

- 先等候幾天或幾週再刪除復原點甚至可能會讓您受益。
- 不同於還原復原點，當您刪除備份資料時，您無法選擇刪除特定的復原點。 如果您選擇刪除備份資料，則會刪除與項目相關聯的所有復原點。

此程序假設 VM 的備份作業已停止或停用。


1. 在[保存庫項目儀表板](backup-azure-manage-vms.md#open-a-vault-item-dashboard)上，按一下 [刪除備份]。

    ![VM 類型](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. 輸入項目的名稱，以確認您想要刪除復原點。

    ![停止驗證](./media/backup-azure-manage-vms/item-verification-box.png)

4. 若要刪除目前項目的備份資料，請按一下![停止備份按鈕](./media/backup-azure-manage-vms/delete-button.png)

    通知訊息可讓您知道備份資料已刪除。

## <a name="next-steps"></a>後續步驟
- [深入了解](backup-azure-vms-first-look-arm.md)從 VM 設定備份 Azure VM。
- [深入了解](backup-azure-arm-restore-vms.md)還原 VM。 
- [深入了解](backup-azure-monitor-vms.md)監視 Azure VM 備份。
 
