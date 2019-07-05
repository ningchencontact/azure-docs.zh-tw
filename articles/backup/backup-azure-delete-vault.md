---
title: 刪除 Azure 中的復原服務保存庫
description: 描述如何刪除復原服務保存庫。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: e195d9a4b9d2bbe21848e083dbccf864188e0790
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508380"
---
# <a name="delete-a-recovery-services-vault"></a>刪除復原服務保存庫

這篇文章描述如何刪除[Azure 備份](backup-overview.md)復原服務保存庫。 它包含移除相依性，刪除保存庫，然後刪除保存庫強制的指示。


## <a name="before-you-start"></a>開始之前

開始之前，請務必了解，您無法刪除具有伺服器的復原服務保存庫註冊，或含有備份的資料。

- 若要依正常程序刪除保存庫，它所包含的伺服器取消註冊、 移除保存庫的資料，然後再刪除保存庫。
- 如果您嘗試刪除保存庫，仍有相依性、 便會發出錯誤訊息，而且您必須手動移除保存庫的相依性，包括：
    - 備份的項目
    - 受保護的伺服器
    - 備份管理伺服器 （Azure 備份伺服器，DPM）![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- 如果您不想要保留在復原服務保存庫中的任何資料，而且想要刪除保存庫，您可以刪除強制的保存庫。
- 如果您嘗試刪除保存庫，但是無法刪除，則保存庫仍會設定為接收備份資料。


## <a name="delete-a-vault-from-the-azure-portal"></a>從 Azure 入口網站中刪除保存庫

1. 開啟保存庫儀表板。  
2. 在 儀表板中，按一下**刪除**。 請確認您想要刪除。

    ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

如果您收到錯誤，請移除[備份項目](#remove-backup-items)，[基礎結構伺服器](#remove-azure-backup-management-servers)，並[復原點](#remove-azure-backup-agent-recovery-points)，然後再刪除保存庫。

![刪除保存庫時發生錯誤](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>刪除復原服務保存庫使用 Azure Resource Manager 用戶端

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 安裝從 chocolatey[此處](https://chocolatey.org/)並安裝 ARMClient 執行下列命令：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登入您的 Azure 帳戶，並執行此命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 入口網站中，蒐集您想要刪除的保存庫的訂用帳戶識別碼和資源群組名稱。

如需有關 ARMClient 命令的詳細資訊，請參閱這[文件](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>使用 Azure Resource Manager 用戶端來刪除復原服務保存庫

1. 執行下列命令，使用您的訂用帳戶識別碼、 資源群組名稱和保存庫名稱。 當您執行此命令會刪除保存庫，如果您沒有任何相依性。

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 如果保存庫不的空的您會收到錯誤，「 如有此保存庫內的現有資源，無法刪除保存庫 」 集。 若要移除受保護的項目 / 容器內的保存庫中，執行下列動作：

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 入口網站中，確認已刪除保存庫。


## <a name="remove-vault-items-and-delete-the-vault"></a>刪除保存庫項目，並刪除保存庫

刪除復原服務保存庫之前，請移除所有相依性。

### <a name="remove-backup-items"></a>移除備份項目

此程序提供範例，示範如何移除 Azure 檔案的備份資料。

1. 按一下 **備份項目** > **Azure 儲存體 （Azure 檔案服務）**

    ![選取備份類型](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. 以滑鼠右鍵按一下 移除，然後按一下 每個 Azure 檔案項目**停止備份**。

    ![選取備份類型](./media/backup-azure-delete-vault/stop-backup-item.png)


3. 在 **停止備份** > **選擇選項**，選取**刪除備份資料**。
4. 輸入項目的名稱，然後按一下**停止備份**。
   - 這會確認您想要刪除的項目。
   - **停止備份**確認之後，就會啟動 按鈕。
   - 如果您保留並不會刪除資料時，您無法刪除保存庫。

     ![刪除備份資料](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. （選擇性） 提供為什麼您要刪除資料的原因，並加入註解。
6. 若要確認刪除作業完成，請檢查 Azure 訊息 ![刪除備份資料](./media/backup-azure-delete-vault/messages.png)上也提供本文中使用的原始碼。
7. 在作業完成之後，服務會傳送一則訊息：**備份的程序已停止，且已刪除備份資料**。
8. 刪除清單中的項目後，在 [備份項目]  功能表上按一下 [重新整理]  ，以查看保存庫中的項目。

      ![刪除備份資料](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="deleting-backup-items-from-management-console"></a>從管理主控台刪除備份項目

若要刪除的備份項目從 備份基礎結構，瀏覽至您的內部部署伺服器管理主控台 （MARS、 Azure 備份伺服器或 SC DPM，取決於受到您後的項目）。

### <a name="for-mars-agent"></a>MARS 代理程式

- 啟動 MARS 管理主控台，請前往**動作**窗格，然後選擇**排程備份**。
- 從**修改或停止排定的備份**精靈中，選擇**停止使用此備份排程並刪除所有儲存的備份**然後按一下**下一步**。

    ![修改或停止排定的備份](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- 從**停止排定的備份**精靈中，按一下**完成**。

    ![停止排定的備份](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- 系統會提示您輸入安全性 pin 碼。 若要產生的 pin 碼，請執行下列步驟：
  - 登入 Azure 入口網站。
  - 瀏覽至 [復原服務保存庫]   > [設定]   > [屬性]  。
  - 在 [安全性 PIN 碼]  底下，按一下 [產生]  。 複製這個 PIN。（此 pin 碼是有效時間只有五分鐘）
- 在 [管理] 主控台 （用戶端應用程式） 中貼上 PIN，然後按一下**Ok**。

  ![安全性 pin 碼](./media/backup-azure-delete-vault/security-pin.png)

- 在 **修改備份進度**精靈，您會看到*刪除的備份資料會保留 14 天。該時間之後，將永久刪除備份資料。*  

    ![刪除備份基礎結構](./media/backup-azure-delete-vault/deleted-backup-data.png)

既然您已從內部部署中刪除備份的項目，完成下列步驟從入口網站：
- 針對 MARS 遵循[移除 Azure Backup agent 復原點](#remove-azure-backup-agent-recovery-points)

### <a name="for-mabs-agent"></a>MABS 代理程式

有不同的方法來停止/刪除線上保護，請執行任何一個方法如下：

**方法 1**

啟動**MABS 管理**主控台。 在 **選取資料保護方式**區段中，取消選取**我想要線上保護**。

  ![選擇資料保護方式](./media/backup-azure-delete-vault/data-protection-method.png)

**方法 2**

若要刪除保護群組，您必須先停止保護該群組。 您可以使用下列程序來停止保護並刪除保護群組。

1.  在 DPM 系統管理員主控台中，按一下**保護**巡覽列上。
2.  在 [顯示] 窗格中，選取您想要移除的保護群組成員。 按一下滑鼠右鍵以選擇**停止保護的群組成員**選項。
3.  從**停止保護**對話方塊中，選取**刪除受保護的資料** > **刪除存放裝置上線**核取方塊，然後按一下 **停止保護**。

    ![刪除線上儲存體](./media/backup-azure-delete-vault/delete-storage-online.png)

受保護的成員狀態現在會變成**非作用中複本可供使用**。

5. 以滑鼠右鍵按一下 非作用中保護群組，然後選取**移除作用中保護**。

    ![除非作用中保護](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. 從**刪除非作用中保護**視窗中，選取**刪除線上儲存體**然後按一下**Ok**。

    ![移除複本，在磁碟上且在線上](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

既然您已從內部部署中刪除備份的項目，完成下列步驟從入口網站：
- 如 MABS 和 DPM，請遵循中的步驟[移除 Azure 備份管理伺服器](#remove-azure-backup-management-servers)。


### <a name="remove-azure-backup-management-servers"></a>移除 Azure 備份管理伺服器

移除之前的 Azure 備份管理伺服器，確定執行中列出的步驟[刪除備份項目從管理主控台](#deleting-backup-items-from-management-console)。

1. 在 [保存庫儀表板] 功能表中，按一下**備份基礎結構**。
2. 按一下 **備份管理伺服器**來檢視伺服器。

    ![選取保存庫以開啟其儀表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 以滑鼠右鍵按一下項目 >**刪除**。
4. 在 [**刪除**] 功能表中，輸入伺服器的名稱，然後按一下**刪除**。

     ![刪除備份資料](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  （選擇性） 提供為什麼您要刪除資料的原因，並加入註解。

> [!NOTE]
> 如果您看到下列錯誤，然後執行步驟中所列的第一個[刪除備份項目從管理主控台](#deleting-backup-items-from-management-console)。
>
>![刪除失敗](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 如果您無法執行步驟，從 [管理] 主控台中刪除備份，比方說，因為無法使用 [管理] 主控台的伺服器連絡 Microsoft 支援服務。

6. 若要確認刪除作業完成，請檢查 Azure 訊息 ![刪除備份資料](./media/backup-azure-delete-vault/messages.png)上也提供本文中使用的原始碼。
7. 在作業完成之後，服務會傳送一則訊息：**備份的程序已停止，且已刪除備份資料**。
8. 刪除項目在清單中，在後**備份基礎結構**功能表上，按一下**重新整理**查看保存庫中的項目。


### <a name="remove-azure-backup-agent-recovery-points"></a>移除 Azure Backup agent 復原點

移除之前的 Azure 備份的復原點，確定執行中列出的步驟[刪除備份項目從管理主控台](#deleting-backup-items-from-management-console)。

1. 在 [保存庫儀表板] 功能表中，按一下**備份基礎結構**。
2. 按一下 **受保護伺服器**若要檢視的基礎結構伺服器。

    ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. 在 [受保護的伺服器]  清單中，按一下 [Azure 備份代理程式]。

    ![選取備份類型](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. 按一下 保護使用 Azure 備份代理程式的伺服器清單中的伺服器。

    ![選取特定受保護的伺服器](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 在選取的伺服器儀表板中，按一下 **刪除**。

    ![刪除選取的伺服器](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. 在 [**刪除**] 功能表中，輸入伺服器的名稱，然後按一下**刪除**。

     ![刪除備份資料](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. （選擇性） 提供為什麼您要刪除資料的原因，並加入註解。

> [!NOTE]
> 如果您看到下列錯誤，然後執行步驟中所列的第一個[刪除備份項目從管理主控台](#deleting-backup-items-from-management-console)。
>
>![刪除失敗](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 如果您無法執行步驟，從 [管理] 主控台中刪除備份，比方說，因為無法使用 [管理] 主控台的伺服器連絡 Microsoft 支援服務。 

8. 若要確認刪除作業完成，請檢查 Azure 訊息 ![刪除備份資料](./media/backup-azure-delete-vault/messages.png)上也提供本文中使用的原始碼。
9. 刪除項目在清單中，在後**備份基礎結構**功能表上，按一下**重新整理**查看保存庫中的項目。


### <a name="delete-the-vault-after-removing-dependencies"></a>移除相依性之後刪除保存庫

1. 當已移除所有相依性時，捲動至**Essentials**保存庫功能表中的窗格。
2. 確認沒有任何**備份項目**，**備份管理伺服器**，或**複寫的項目**列出。 如果項目仍會出現在保存庫，請將它們移除。

3. 當保存庫中沒有其他項目時，在保存庫儀表板中按一下 [刪除]  。

    ![刪除備份資料](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 若要確認您想要刪除保存庫，請按一下 [是]  。 保存庫便會被刪除，入口網站會回到 [新增]  服務功能表。

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>如果我停止備份程序但卻保留了資料，會怎麼樣？

如果您停止備份程序，但是不小心保留資料，您必須先刪除備份資料，如先前各節中所述。

## <a name="next-steps"></a>後續步驟

[深入了解](backup-azure-recovery-services-vault-overview.md)復原服務保存庫。
