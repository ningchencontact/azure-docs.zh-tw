---
title: 還原 Azure 檔案共用
description: 瞭解如何使用 Azure 入口網站從 Azure 備份服務所建立的還原點還原整個檔案共用或特定檔案。
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294392"
---
# <a name="restore-azure-file-shares"></a>還原 Azure 檔案共用

本文說明如何使用 Azure 入口網站，從[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)服務所建立的還原點還原整個檔案共用或特定檔案。

在本指南中，您將瞭解如何：

* 還原完整的 Azure 檔案共用
* 還原個別的檔案或資料夾
* 追蹤還原作業狀態

## <a name="steps-to-perform-restore"></a>執行還原的步驟

### <a name="select-the-file-share-to-restore"></a>選取要還原的檔案共用

1. 在  [Azure 入口網站](https://portal.azure.com/)中，開啟您用來設定檔案共用備份的復原服務保存庫。

2. 在 [**總覽**] 分頁的 [**受保護的專案**] 區段底下，按一下 [**備份專案**]。

    ![按一下 [備份專案]](./media/restore-afs/backup-items.png)

3. 一旦您按一下 [**備份專案**]，就會在 [**總覽**] 分頁旁顯示所有備份管理類型的新分頁，如下所示：

    ![備份管理類型](./media/restore-afs/backup-management.png)

4. 在 [備份**專案**] 的 [**備份管理類型**] 底下，選取 **[Azure 儲存體（Azure 檔案儲存體）** ]。 您會看到所有檔案共用的清單，以及其對應的儲存體帳戶（使用此保存庫備份）。

    ![所有檔案共用的清單](./media/restore-afs/file-shares.png)

5. 從 Azure 檔案共用清單中，選取您想要對其執行還原作業的檔案共用。

### <a name="full-share-recovery"></a>完整共用復原

您可以使用此還原選項，在原始或替代位置中還原完整的檔案共用。

1. 在選取要還原的檔案[共用](#select-the-file-share-to-restore)一節的步驟5中，選取所需的檔案共用，然後從出現的 [**備份專案**] 分頁中，選取 [**還原共用**] 選項。

   ![選取還原共用](./media/restore-afs/restore-share.png)

2. 一旦您按一下 [**還原] 共用**，[**還原**] 分頁隨即開啟，其中會顯示 [還原**點**] 功能表，其中會顯示所選檔案共用可用的還原點清單。

3. 選取您要用於執行還原作業的還原點，然後按一下 **[確定]** 。

    ![選取還原點](./media/restore-afs/restore-point.png)

4. 一旦您按一下 [確定]，[還原] 分頁功能表就會切換到 [**還原位置**]。 在 [**還原位置**] 中，指定還原資料的位置（或如何）。 您可以在以下兩個選項中選擇其中一項：

    * **原始位置**：將完整的檔案共用還原到與原始來源相同的位置。
    * **替代位置**：將完整的檔案共用還原至替代位置，並保留原始的檔案**共用。**

#### <a name="restore-to-original-location"></a>還原至原始位置

1. 選擇 [**原始位置**] 作為復原**目的地**，然後選取是否要在發生衝突時略過或覆寫。 做出適當的選擇之後，請按一下 **[確定]** 。

    ![選擇原始位置](./media/restore-afs/original-location.png)

2. 按一下 [**還原**] 以啟動還原作業。

    ![按一下 [還原] 開始](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 選擇 [**替代位置**] 作為復原目的地。
2. 從 [**儲存體帳戶**] 欄位的下拉式功能表中，選取您想要還原備份內容的目的地儲存體帳戶。
3. 根據您在步驟2中選取的儲存體帳戶，[**選取檔案共用**] 下拉式功能表會顯示出現在所選儲存體帳戶中的檔案共用清單。 選取您要還原備份內容的檔案共用。
4. 在 [**資料夾名稱**] 欄位中，指定您想要在目的地檔案共用中建立的資料夾名稱和還原的內容。
5. 選取是否要在發生衝突時略過或覆寫。
6. 在所有欄位中輸入適當的值之後，請按一下 **[確定]** 。

    ![選取替代位置](./media/restore-afs/alternate-location.png)

7. 按一下 [還原] 以啟動還原作業。

    ![按一下 [還原] 開始](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>專案層級復原

您可以使用此還原選項來還原原始或替代位置中的個別檔案或資料夾。

1. 在選取要還原的檔案[共用](#select-the-file-share-to-restore)一節的步驟5中，選取所需的檔案共用，然後從出現的 [**備份專案**] 分頁中選取 [檔案復原 **] 選項。**

    ![選取檔案復原](./media/restore-afs/file-recovery.png)

2. 一旦**您按一下 [** 檔案復原]，[**還原**] 分頁隨即開啟，其中會顯示一個 [**還原點**] 功能表，其中包含適用于所選檔案共用的還原點清單。

3. 選取您要用於執行還原作業的還原點，然後按一下 **[確定]** 。

    ![選取還原點](./media/restore-afs/restore-point.png)

4. 一旦您按一下 [確定]，[還原] 分頁功能表就會切換到 [**還原位置**]。 在 [**還原位置**] 中，指定還原資料的位置（或如何）。 您可以在以下兩個選項中選擇其中一項：

    * **原始位置**：將選取的檔案/資料夾還原到與原始來源相同的檔案共用。
    * **替代位置**：將選取的檔案/資料夾還原至替代位置，並保留原始的檔案**共用內容。**

#### <a name="restore-to-original-location"></a>還原至原始位置

1. 選擇 [**原始位置**] 作為復原**目的地**，然後選取是否要在發生衝突時略過或覆寫。

    ![專案層級復原的原始位置](./media/restore-afs/original-location-item-level.png)

2. 按一下 [**選取**檔案]，選擇您想要還原的檔案/資料夾。

    ![按一下 [選取檔案]](./media/restore-afs/select-file.png)

3. 當您按一下 [**選取**檔案] 時，就會顯示 [檔案共用] 分頁（顯示您選取要還原的檔案共用復原點內容）。

4. 選取對應至您要還原之檔案/資料夾的方塊，然後按一下 [**選取**]。

    ![選取檔案或資料夾](./media/restore-afs/select-file-folder.png)

5. 重複步驟2-4，選取多個檔案/資料夾進行還原。
6. 選取您想要還原的所有專案之後，請按一下 **[確定]** 。

    ![選取要還原的所有專案之後，請按一下 [確定]](./media/restore-afs/after-selecting-items.png)

7. 按一下 [還原] 以啟動還原作業。

    ![按一下 [還原] 開始](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 選擇 [**替代位置**] 作為復原目的地。
2. 從 [**儲存體帳戶**] 欄位的下拉式功能表中，選取您想要還原備份內容的目的地儲存體帳戶。
3. 根據您在步驟2中選取的儲存體帳戶，[**選取檔案共用**] 下拉式功能表會顯示所選儲存體帳戶中的檔案共用清單。 選取您要還原備份內容的檔案共用。
4. 在 [**資料夾名稱**] 欄位中，指定您想要在目的地檔案共用中建立的資料夾名稱和還原的內容。
5. 選取是否要在發生衝突時略過或覆寫。
6. 按一下 [**選取**檔案]，選擇您想要還原的檔案/資料夾。

    ![選取要還原至替代位置的專案](./media/restore-afs/restore-to-alternate-location.png)

7. 當您按一下 [**選取**檔案] 時，就會顯示 [檔案共用] 分頁（顯示您選取要還原的檔案共用復原點內容）。
8. 選取對應至您要還原之檔案/資料夾的方塊，然後按一下 [**選取**]。

    ![選取復原目的地](./media/restore-afs/recovery-destination.png)

9. 重複步驟6-8，選取多個檔案/資料夾進行還原。
10. 選取您想要還原的所有專案之後，請按一下 **[確定]** 。

    [選取所有檔案之後，按一下 [確定]](./media/restore-afs/after-selecting-all-items.png)

11. 按一下 [**還原**] 以啟動還原作業。

## <a name="track-restore-operation"></a>追蹤還原作業

在觸發還原作業之後，備份服務會建立用於追蹤的作業。 Azure 備份會在入口網站中顯示作業的相關通知。 若要檢視作業的運作，請按一下 [通知] 超連結。

![按一下 [通知] 超連結](./media/restore-afs/notifications-link.png)

您也可以從復原服務保存庫監視還原進度。 以下是檢查還原作業狀態的步驟：

1. 開啟復原服務保存庫，讓您從中觸發還原作業。
2. 在 [**總覽**] 分頁的 [**監視] 區段**底下，按一下 [**備份作業**]，以查看針對不同工作負載執行的操作狀態。

    ![按一下 [備份作業]](./media/restore-afs/backup-jobs.png)

3. 按一下對應至您檔案共用的工作負載名稱，以查看更多關於還原作業的詳細資料，例如傳輸的資料、還原的檔案數目等等。

    ![請參閱還原的詳細資料](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何[管理 Azure 檔案共用備份](manage-afs-backup.md)
