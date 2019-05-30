---
title: Azure 備份：建立復原服務保存庫
description: 建立復原服務保存庫儲存的備份與復原點
services: backup
author: sogup
manager: vijayts
keywords: 復原服務保存庫;Azure VM 備份;Azure VM 還原;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: sogup
ms.openlocfilehash: b345f5e7f6f32d7fbb76d4f0227c8f4f925ff884
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400292"
---
# <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

復原服務保存庫是一個實體，可儲存一段時間以來建立的備份和復原點。 復原服務保存庫也包含與受保護虛擬機器相關聯的備份原則。

若要建立復原服務保存庫：

1. 在 [Azure 入口網站](https://portal.azure.com/)中登入您的訂用帳戶。

2. 在左側功能表上，選取 [所有服務]  。

    ![選取所有服務](./media/backup-create-rs-vault/click-all-services.png)

3. 在 [所有服務]  對話方塊中，輸入 **Recovery Services**。 資源清單會根據您的輸入進行篩選。 在資源清單中，選取 [復原服務保存庫]  。

    ![輸入並選擇復原服務保存庫](./media/backup-create-rs-vault/all-services.png)

    隨即會在訂用帳戶中出現 [復原服務保存庫] 清單。

4. 在 [復原服務保存庫]  儀表板上，選取 [新增]  。

    ![新增復原服務保存庫](./media/backup-create-rs-vault/add-button-create-vault.png)

    [復原服務保存庫]  對話方塊隨即開啟。 提供 [名稱]  、[訂用帳戶]  、[資源群組]  和 [位置]  的值。

    ![設定復原服務保存庫](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **名稱**：輸入可識別保存庫的易記名稱。 該名稱必須是 Azure 訂用帳戶中唯一的名稱。 指定的名稱至少要有兩個字元，但不能超過 50 個字元。 名稱開頭必須是字母，且只能包含字母、數字和連字號。
   - 訂用帳戶  ：選擇要使用的訂用帳戶。 如果您是唯一一個訂用帳戶的成員，就會看到該名稱。 如果您不確定要使用哪個訂用帳戶，請使用預設 (建議) 的訂用帳戶。 只有在您的公司或學校帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。
   - **资源组**：使用現有的資源群組或建立新群組。 若要查看您訂用帳戶中可用的資源群組清單，請選取 [使用現有的]  ﹐然後從下拉式清單方塊中選取資源。 若要建立新的資源群組，請選取 [新建]  ，然後輸入名稱。 如需資源群組的完整資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
   - **位置**：選取保存庫的地理區域。 若要要建立保存庫來保護虛擬機器，則保存庫**必須**與虛擬機器位於相同區域。

      > [!IMPORTANT]
      > 如果不確定您 VM 的位置，請關閉對話方塊。 移至入口網站中的虛擬機器清單。 如果您在數個區域中有虛擬機器，請在每個區域中建立復原服務保存庫。 請在第一個位置建立保存庫，然後再建立另一個位置的保存庫。 不需要指定用來儲存備份資料的儲存體帳戶。 復原服務保存庫和 Azure 備份服務會自動處理該事宜。
      >
      >

5. 當您準備好建立復原服務保存庫時，選取 [建立]  。

    ![建立復原服務保存庫](./media/backup-create-rs-vault/click-create-button.png)

    建立復原服務保存庫可能需要一些時間。 請監視入口網站右上角 [通知]  區域中的狀態通知。 保存庫建立之後，就可以在 [復原服務保存庫] 的清單中看到。 如果您沒有看到保存庫，請選取 [重新整理]  。

     ![重新整理備份保存庫的清單](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>設定儲存體備援

Azure 備份會自動處理保存庫的儲存體。 您必須指定該儲存體的複寫方式。

1. 從 [復原服務保存庫]  刀鋒視窗，按一下 [新增保存庫]。 底下**設定**區段中，按一下**屬性**。
2. 在 **屬性**下方**備份設定**，按一下**更新**。

3. 選取儲存體複寫類型，然後按一下**儲存**。

     ![為新保存庫設定儲存體組態](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - 我們建議，如果您使用 Azure 作為主要的備份儲存體端點，繼續使用預設值**異地備援**設定。
   - 如果您未使用 Azure 做為主要的備份儲存體端點，則選擇 [本地備援]  ，以減少 Azure 儲存體成本。
   - 深入了解[異地](../storage/common/storage-redundancy-grs.md)並[本機](../storage/common/storage-redundancy-lrs.md)備援。

> [!NOTE]
> 一旦您選擇**儲存體複寫類型**從**read-access geo-redundant**來**本地備援**，您無法還原。

## <a name="next-steps"></a>後續步驟

[深入了解](backup-azure-recovery-services-vault-overview.md)復原服務保存庫。
[深入了解](backup-azure-delete-vault.md)刪除復原服務保存庫。
