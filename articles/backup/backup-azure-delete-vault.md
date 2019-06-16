---
title: 刪除 Azure 中的復原服務保存庫
description: 描述如何刪除復原服務保存庫。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: raynew
ms.openlocfilehash: a7dd5530c3941fe55e8a649f8adb217159823f5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66492781"
---
# <a name="delete-a-recovery-services-vault"></a>刪除復原服務保存庫

這篇文章描述如何刪除[Azure 備份](backup-overview.md)復原服務保存庫。 它包含移除相依性，刪除保存庫，然後刪除保存庫強制的指示。


## <a name="before-you-start"></a>開始之前

開始之前，請務必了解，您無法刪除具有伺服器的復原服務保存庫註冊，或含有備份的資料。

- 若要依正常程序刪除保存庫，您可以裡面的伺服器取消註冊、 移除保存庫的資料，並再刪除保存庫。
- 如果您嘗試刪除仍有相依性的保存庫時，會發出錯誤訊息。 而且，您需要手動移除保存庫的相依性，包括：
    - 備份的項目
    - 受保護的伺服器
    - 備份管理伺服器 （Azure 備份伺服器，DPM）![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- 如果您不想要保留在復原服務保存庫中的任何資料，而且想要刪除保存庫，您可以刪除強制的保存庫。
- 如果您嘗試刪除保存庫，但是無法刪除，則保存庫仍會設定為接收備份資料。


## <a name="delete-a-vault-from-the-azure-portal"></a>從 Azure 入口網站中刪除保存庫

1. 開啟保存庫儀表板。  
2. 在 儀表板中，按一下**刪除**。 請確認您想要刪除。

    ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

如果您收到錯誤，請移除[備份項目](#remove-backup-items)，[基礎結構伺服器](#remove-backup-infrastructure-servers)，並[復原點](#remove-azure-backup-agent-recovery-points)，然後再刪除保存庫。

![刪除保存庫時發生錯誤](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>刪除復原服務保存庫使用 Azure Resource Manager 用戶端

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 安裝從 chocolatey[此處](https://chocolatey.org/)並安裝 ARMClient 執行下列命令：

   ` choco install armclient --source=https://chocolatey.org/api/v2/ `
2. 登入 Azure 帳戶，執行下列命令

    ` ARMClient.exe login [environment name] `

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

這些程序提供一些範例移除備份資料和基礎結構伺服器。 從保存庫移除所有項目之後，您可以將它刪除。

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

5. 選擇性地提供的原因為何您要刪除資料，並加入註解。
6. 若要確認刪除作業完成，請檢查 Azure 訊息 ![刪除備份資料](./media/backup-azure-delete-vault/messages.png)上也提供本文中使用的原始碼。
7. 在作業完成之後，服務會傳送一則訊息：**備份的程序已停止，且已刪除備份資料**。
8. 刪除清單中的項目後，在 [備份項目]  功能表上按一下 [重新整理]  ，以查看保存庫中的項目。

      ![刪除備份資料](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>移除備份基礎結構伺服器

1. 在 [保存庫儀表板] 功能表中，按一下**備份基礎結構**。
2. 按一下 **備份管理伺服器**來檢視伺服器。

    ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 以滑鼠右鍵按一下項目 >**刪除**。
4. 若要確認刪除作業完成，請檢查 Azure 訊息 ![刪除備份資料](./media/backup-azure-delete-vault/messages.png)上也提供本文中使用的原始碼。
5. 在作業完成之後，服務會傳送一則訊息：**備份的程序已停止，且已刪除備份資料**。
6. 刪除項目在清單中，在後**備份基礎結構**功能表上，按一下**重新整理**查看保存庫中的項目。


### <a name="remove-azure-backup-agent-recovery-points"></a>移除 Azure Backup agent 復原點

1. 在 [保存庫儀表板] 功能表中，按一下**備份基礎結構**。
2. 按一下 **備份管理伺服器**若要檢視的基礎結構伺服器。

    ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. 在 [受保護的伺服器]  清單中，按一下 [Azure 備份代理程式]。

    ![選取備份類型](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. 按一下 保護使用 Azure 備份代理程式的伺服器清單中的伺服器。

    ![選取特定受保護的伺服器](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 在選取的伺服器儀表板中，按一下 **刪除**。

    ![刪除選取的伺服器](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. 在 [刪除]  功能表中，輸入項目的名稱，然後按一下 [刪除]  。

     ![刪除備份資料](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. 選擇性地提供的原因為何您要刪除資料，並加入註解。
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
