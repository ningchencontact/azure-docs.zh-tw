---
title: 管理 Azure 檔案共用備份
description: 本文說明管理和監視由 Azure 備份服務所備份之 Azure 檔案共用的一般工作。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294548"
---
# <a name="manage-azure-file-share-backups"></a>管理 Azure 檔案共用備份

本文說明管理和監視由[Azure 備份](https://docs.microsoft.com/azure/backup/backup-overview)服務所備份之 Azure 檔案共用的一般工作。 您將瞭解如何在復原服務保存庫中執行下列管理工作：

* [監視工作](#monitor-jobs)
* [建立新的原則](#create-a-new-policy)
* [修改原則](#modify-policy)
* [停止保護檔案共用](#stop-protection-on-a-file-share)
* [繼續保護檔案共用](#resume-protection-on-a-file-share)
* [刪除備份資料](#delete-backup-data)
* [取消註冊儲存體帳戶](#unregister-storage-account)

## <a name="monitor-jobs"></a>監視工作

當您觸發備份或還原作業時，備份服務會建立追蹤的工作。 您可以在 [備份作業] 頁面上監視所有作業的進度。

若要開啟 [備份作業] 頁面：

1. 開啟您用來設定檔案共用備份的復原服務保存庫。 在 [**總覽**] 分頁中，按一下 [**監視**] 區段底下的 [**備份作業**]。

   ![監視區段中的備份作業](./media/manage-afs-backup/backup-jobs.png)

2. 一旦您按一下 [確定]，就會顯示 [**備份作業**] 分頁，列出所有作業的狀態。 您可以按一下對應于您要監視之檔案共用的工作負載名稱。

   ![工作負載名稱](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>建立新的原則

您可以建立新的原則，以從復原服務保存庫的 [**備份原則**] 區段備份 Azure 檔案共用。 當您為檔案共用設定備份時所建立的所有原則，都會以原則類型顯示為 Azure 檔案共用。

若要檢視現有的備份原則：

1. 開啟您用來設定檔案共用備份的復原服務保存庫，然後在 [復原服務保存庫] 功能表中，按一下 [管理] 區段下的 [**備份原則**]。 保存庫中設定的所有備份原則都會列出。

   ![所有備份原則](./media/manage-afs-backup/all-backup-policies.png)

2. 若要查看 Azure 檔案共用特有的原則，請從右上角的下拉式選單中選取 [ **Azure 檔案共用**]。

   ![選擇 Azure 檔案共用](./media/manage-afs-backup/azure-file-share.png)

若要建立新的備份原則：

1. 按一下 [ **+ 新增**備份原則] 分頁。

   ![新增備份原則](./media/manage-afs-backup/new-backup-policy.png)

2. 在 [**新增**] 分頁中選取 [ **Azure 檔案共用**] 作為**原則類型**。 Azure 檔案共用的 [備份原則] 分頁隨即開啟。 指定復原點的原則名稱、備份頻率和保留範圍。 定義原則之後，請按一下 **[確定]** 。

   ![定義備份原則](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>修改原則

您可以修改備份原則來變更備份頻率或保留範圍。

若要修改原則：

1. 開啟您用來設定檔案共用備份的復原服務保存庫，然後在 [復原服務保存庫] 功能表中，按一下 [管理] 區段下的 [**備份原則**]。 保存庫中設定的所有備份原則都會列出。

   ![保存庫中的所有備份原則](./media/manage-afs-backup/all-backup-policies-modify.png)

2. 若要查看 Azure 檔案共用特有的原則，請從右上角的下拉式選單中選取 [ **Azure 檔案共用**]。 按一下您要修改的備份原則。

   ![要修改的 Azure 檔案共用](./media/manage-afs-backup/azure-file-share-modify.png)

3. [**排程**] 分頁隨即開啟。 視需要編輯 [備份排程]/[保留範圍]，然後按一下 [**儲存**]。 您會在分頁上看到「更新進行中」訊息，而當原則變更成功更新時，您會看到「已成功更新備份原則」訊息。

   ![儲存修改過的原則](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>停止保護檔案共用

有兩種方式可停止保護 Azure 檔案共用︰

* 停止所有未來的備份作業並*刪除所有復原點*
* 停止所有未來的備份工作 *，但保留復原點*

在儲存體中保留復原點可能會產生相關成本，因為將會保留 Azure 備份所建立的基礎快照集。 不過，保留復原點的好處是以後可以在需要時還原檔案共用。 如需復原點保留成本的相關資訊，請參閱 [定價詳細資料](https://azure.microsoft.com/pricing/details/backup/)。 如果您選擇刪除所有復原點，則無法還原檔案共用。

若要停止保護 Azure 檔案共用：

1. 開啟包含檔案共用復原點的復原服務保存庫，然後按一下 [受保護的專案] 區段下的 [**備份專案**]。 [備份項目] 類型清單隨即出現。

   ![備份項目](./media/manage-afs-backup/backup-items.png)

2. 在 [備份管理類型] 清單中，選取 [Azure 儲存體 (Azure 檔案服務)]。 **（Azure 儲存體（Azure 檔案儲存體））的備份專案**清單隨即出現。

   ![選取 Azure 儲存體（Azure 檔案儲存體）](./media/manage-afs-backup/azure-storage-azure-files.png)

3. 在**備份專案清單（Azure 儲存體（Azure 檔案儲存體））** 中，選取您要停止保護的備份專案。

4. 從 [**備份專案**] 分頁的功能表中，選取 [**停止備份**] 選項。

   ![選取 [停止備份]](./media/manage-afs-backup/stop-backup.png)

5. 在 [**停止備份**] 分頁中，選擇 [**保留備份資料**] 或 [**刪除備份資料**]，然後按一下 [**停止備份**]。

    ![選擇保留或刪除備份資料](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>繼續保護檔案共用

如果在已停止保護檔案共用時選擇了 [**保留備份資料**] 選項，則可以繼續保護。 如果已選擇 [刪除備份資料] 選項，則無法繼續保護檔案共用。

若要繼續保護 Azure 檔案共用：

1. 開啟包含檔案共用復原點的復原服務保存庫，然後按一下 [受保護的專案] 區段下的 [**備份專案**]。 [備份項目] 類型清單隨即出現。

   ![繼續的備份專案](./media/manage-afs-backup/backup-items-resume.png)

2. 在 [備份管理類型] 清單中，選取 [Azure 儲存體 (Azure 檔案服務)]。 **（Azure 儲存體（Azure 檔案儲存體））的備份專案**清單隨即出現。

   ![Azure 儲存體（Azure 檔案儲存體）清單](./media/manage-afs-backup/azure-storage-azure-files.png)

3. 在**備份專案清單（Azure 儲存體（Azure 檔案儲存體））** 中，選取您要繼續保護的備份專案。

4. 從 [**備份專案**] 分頁的功能表中選取 [**繼續備份**] 選項。

   ![選取 [繼續備份]](./media/manage-afs-backup/resume-backup.png)

5. [**備份原則**] 分頁隨即開啟，您可以選擇您選擇的原則來繼續備份。

6. 選取所需的**備份原則**之後，請按一下 [**儲存**]。 您會在入口網站上看到「更新進行中」訊息，一旦成功繼續備份，您就會看到「已成功更新受保護 Azure 檔案共用的備份原則」訊息。

   ![已成功更新備份原則](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>刪除備份資料

您可以在**停止備份**作業期間，或在停止保護後的任何時間，刪除檔案共用的備份。 先等候幾天或幾週再刪除復原點甚至可能會讓您受益。 刪除備份資料時，您無法選擇要刪除的特定復原點。 如果您選擇刪除備份資料，則會刪除與檔案共用相關聯的所有復原點。

下列程式假設已停止檔案共用的保護。

若要刪除 Azure 檔案共用的備份資料：

1. 一旦備份作業停止，[**備份專案**] 儀表板中就會有 [**繼續備份**] 和 [**刪除備份資料**] 選項。 在 [**備份專案**] 分頁的功能表中，按一下 [**刪除備份資料**] 選項。

   ![刪除備份資料](./media/manage-afs-backup/delete-backup-data.png)

2. [**刪除備份資料**] 分頁隨即開啟。 輸入檔案共用的名稱，以確認刪除。 （選擇性）提供刪除或**批註**的**原因**。 一旦您確定刪除備份資料，請按一下 [**刪除**]。

   ![確認刪除資料](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>取消註冊儲存體帳戶

如果您想要使用不同的復原服務保存庫來保護特定儲存體帳戶中的檔案共用，請先[停止保護](#stop-protection-on-a-file-share)該儲存體帳戶中的所有檔案共用。 然後，從目前用於保護的復原服務保存庫中取消註冊該帳戶。

下列程式假設您想要取消註冊之儲存體帳戶中的所有檔案共用都已停止保護。

若要取消註冊儲存體帳戶：

1. 開啟您的儲存體帳戶註冊所在的復原服務保存庫。
2. 在 [**總覽**] 分頁的 [**管理**] 區段下，按一下 [**備份基礎結構**] 選項。

   ![按一下 [備份基礎結構]](./media/manage-afs-backup/backup-infrastructure.png)

3. [**備份基礎結構**] 分頁隨即開啟。 在此分頁的 [ **Azure 儲存體帳戶**] 區段下，按一下 [**儲存體帳戶**]。

   ![按一下 [儲存體帳戶]](./media/manage-afs-backup/storage-accounts.png)

4. 一旦您按一下 [**儲存體帳戶**]，就會顯示向保存庫註冊的儲存體帳戶清單。
5. 以滑鼠右鍵按一下您要取消註冊的儲存體帳戶，然後選擇 [**取消註冊**]。

   ![選取 [取消註冊]](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[針對 Azure 檔案共用的備份/還原失敗進行疑難排解](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)
