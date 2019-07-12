---
title: 刪除 Azure 中的復原服務保存庫
description: 描述如何刪除復原服務保存庫。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 01c20ce84f5c97b3a0ac437fe602861085b5052c
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827891"
---
# <a name="delete-a-recovery-services-vault"></a>刪除復原服務保存庫

這篇文章描述如何刪除[Azure 備份](backup-overview.md)復原服務保存庫。 它包含移除相依性，然後刪除保存庫的指示。


## <a name="before-you-start"></a>開始之前

您無法刪除具有相依性，例如受保護的伺服器或與保存庫相關聯的備份管理伺服器的復原服務保存庫。<br/>
無法刪除保存庫包含備份資料，（也就是即使您已停止保護，但保留備份資料）。

如果您刪除包含相依性的保存庫時，它就會顯示下列錯誤：

![刪除保存庫時發生錯誤](./media/backup-azure-delete-vault/error.png)

依正常程序刪除保存庫會依照下面的順序中所述的步驟：
- 停止保護並且刪除備份資料
- 刪除受保護的伺服器或備份管理伺服器
- 刪除保存庫


## <a name="delete-backup-data-and-backup-items"></a>刪除備份資料和備份的項目

再繼續進行進一步的讀取 **[這](#before-you-start)** 區段來了解相依性，並在保存庫刪除程序。

### <a name="for-protected-items-in-cloud"></a>在雲端中的受保護項目

若要停止保護並刪除備份資料，請執行如下：

1. 從入口網站 > 復原服務保存庫 > 備份項目選擇在雲端中的 受保護的項目。

    ![選取備份類型](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. 針對每個項目，您需要以滑鼠右鍵按一下，然後選擇 **停止備份**。

    ![選取備份類型](./media/backup-azure-delete-vault/stop-backup-item.png)

3. 在 **停止備份** > **選擇選項**，選取**刪除備份資料**。
4. 輸入項目的名稱，然後按一下**停止備份**。
   - 這會確認您想要刪除的項目。
   - **停止備份**確認之後，就會啟動 按鈕。
   - 如果您保留並不會刪除資料時，您無法刪除保存庫。

     ![刪除備份資料](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 請檢查**通知**![刪除備份資料](./media/backup-azure-delete-vault/messages.png)。 完成之後，服務會顯示訊息：**停止備份並刪除備份資料 」*備份項目*"** 。 **已成功完成作業**。
6. 按一下 **重新整理**上**備份項目**功能表上，以檢查是否要移除 備份項目。

      ![刪除備份資料](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>MARS 代理程式

若要停止保護並刪除備份資料，請在下面所列的順序執行步驟：

- [步驟 1：從 MARS 管理主控台刪除備份項目](#step-1-delete-backup-items-from-mars-management-console)
- [步驟 2：從入口網站移除 Azure Backup agent](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>步驟 1：從 MARS 管理主控台刪除備份項目

如果您無法執行此步驟，因為伺服器無法使用，然後連絡 Microsoft 支援服務。

- 啟動 MARS 管理主控台，請前往**動作**窗格，然後選擇**排程備份**。
- 從**修改或停止排定的備份**精靈中，選擇**停止使用此備份排程並刪除所有儲存的備份**然後按一下**下一步**。

    ![修改或停止排定的備份](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- 從**停止排定的備份**精靈中，按一下**完成**。

    ![停止排定的備份](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- 系統會提示您輸入安全性 pin 碼。 若要產生的 pin 碼，請執行下列步驟：
  - 登入 Azure 入口網站。
  - 瀏覽至 [復原服務保存庫]   > [設定]   > [屬性]  。
  - 在 [安全性 PIN 碼]  底下，按一下 [產生]  。 複製這個 PIN。（此 pin 碼，為有效時間只有五分鐘）
- 在 [管理] 主控台 （用戶端應用程式） 中貼上 PIN，然後按一下**Ok**。

  ![安全性 pin 碼](./media/backup-azure-delete-vault/security-pin.png)

- 在 **修改備份進度**精靈，您會看到*刪除的備份資料會保留 14 天。該時間之後，將永久刪除備份資料。*  

    ![刪除備份基礎結構](./media/backup-azure-delete-vault/deleted-backup-data.png)

既然您已刪除的備份項目，從內部部署，請完成接下來的步驟，從入口網站。

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>步驟 2：從入口網站移除 Azure Backup Agent

請確定[步驟 1](#step-1-delete-backup-items-from-mars-management-console)完成之後，才繼續進行：

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

> [!NOTE]
> 如果您看到下列錯誤，然後執行步驟中所列的第一個[刪除備份項目從管理主控台](#step-1-delete-backup-items-from-mars-management-console)。
>
>![刪除失敗](./media/backup-azure-delete-vault/deletion-failed.png)
>
>如果您無法執行步驟，從 [管理] 主控台中刪除備份，比方說，因為無法使用 [管理] 主控台的伺服器連絡 Microsoft 支援服務。

7. 請檢查**通知**![刪除備份資料](./media/backup-azure-delete-vault/messages.png)。 完成之後，服務會顯示訊息：**停止備份並刪除備份資料 」*備份項目*"** 。 **已成功完成作業**。
8. 按一下 **重新整理**上**備份項目**功能表上，以檢查是否要移除 備份項目。


### <a name="for-mabs-agent"></a>MABS 代理程式

若要停止保護並刪除備份資料，請在下面所列的順序執行步驟：

- [步驟 1：從 MABS 管理主控台中刪除備份項目](#step-1-delete-backup-items-from-mabs-management-console)
- [步驟 2：從入口網站中，移除 Azure 備份管理伺服器](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>步驟 1：從 MABS 管理主控台中刪除備份項目

如果您無法執行此步驟，因為伺服器無法使用，然後連絡 Microsoft 支援服務。

**方法 1**若要停止保護並刪除備份資料，請執行下列步驟：

1.  在 DPM 系統管理員主控台中，按一下**保護**巡覽列上。
2.  在 [顯示] 窗格中，選取您想要移除的保護群組成員。 按一下滑鼠右鍵以選擇**停止保護的群組成員**選項。
3.  從**停止保護**對話方塊中，選取**刪除受保護的資料** > **刪除存放裝置上線**核取方塊，然後按一下 **停止保護**。

    ![刪除線上儲存體](./media/backup-azure-delete-vault/delete-storage-online.png)

受保護的成員狀態現在會變成**非作用中複本可供使用**。

5. 以滑鼠右鍵按一下 非作用中保護群組，然後選取**移除作用中保護**。

    ![除非作用中保護](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. 從**刪除非作用中保護**視窗中，選取**刪除線上儲存體**然後按一下**Ok**。

    ![移除複本，在磁碟上且在線上](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**方法 2**啟動**MABS 管理**主控台。 在 **選取資料保護方式**區段中，取消選取**我想要線上保護**。

  ![選擇資料保護方式](./media/backup-azure-delete-vault/data-protection-method.png)

既然您已刪除的備份項目，從內部部署，請完成接下來的步驟，從入口網站。

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>步驟 2：從入口網站中，移除 Azure 備份管理伺服器

請確定[步驟 1](#step-1-delete-backup-items-from-mabs-management-console)完成之後，才繼續進行：

1. 在 [保存庫儀表板] 功能表中，按一下**備份基礎結構**。
2. 按一下 **備份管理伺服器**來檢視伺服器。

    ![選取保存庫以開啟其儀表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 以滑鼠右鍵按一下項目 >**刪除**。
4. 在 [**刪除**] 功能表中，輸入伺服器的名稱，然後按一下**刪除**。

     ![刪除備份資料](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> 如果您看到下列錯誤，然後執行步驟中所列的第一個[刪除備份項目從管理主控台](#step-2-from-portal-remove-azure-backup-management-servers)。
>
>![刪除失敗](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 如果您無法執行步驟，從 [管理] 主控台中刪除備份，比方說，因為無法使用 [管理] 主控台的伺服器連絡 Microsoft 支援服務。

5. 請檢查**通知**![刪除備份資料](./media/backup-azure-delete-vault/messages.png)。 完成之後，服務會顯示訊息：**停止備份並刪除備份資料 」*備份項目*"** 。 **已成功完成作業**。
6. 按一下 **重新整理**上**備份項目**功能表上，以檢查是否要移除 備份項目。


## <a name="delete-the-recovery-services-vault"></a>刪除復原服務保存庫

1. 當已移除所有相依性時，捲動至**Essentials**保存庫功能表中的窗格。
2. 確認沒有任何**備份項目**，**備份管理伺服器**，或**複寫的項目**列出。 如果項目仍會出現在保存庫中，[將它們移除](#delete-backup-data-and-backup-items)。

3. 當保存庫中沒有其他項目時，在保存庫儀表板中按一下 [刪除]  。

    ![刪除備份資料](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 若要確認您想要刪除保存庫，請按一下 [是]  。 保存庫便會被刪除，入口網站會回到 [新增]  服務功能表。

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>刪除復原服務保存庫使用 Azure Resource Manager 用戶端

刪除復原服務保存庫僅建議使用此選項時，會移除所有相依性和依舊*保存庫刪除錯誤*。



- 從**Essentials**窗格中的保存庫功能表中，確認沒有任何**備份項目**，**備份管理伺服器**，或**複寫的項目**列出。 如果沒有備份的項目，則執行中的步驟[刪除備份資料和備份的項目](#delete-backup-data-and-backup-items)。
- 重試[從入口網站中刪除保存庫](#delete-the-recovery-services-vault)。
- 如果移除所有相依性，您依舊*保存庫刪除錯誤*接著使用 ARMClient 工具來執行步驟如下所示。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 安裝從 chocolatey[此處](https://chocolatey.org/)並安裝 ARMClient 執行下列命令：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登入您的 Azure 帳戶，並執行此命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 入口網站中，蒐集您想要刪除的保存庫的訂用帳戶識別碼和資源群組名稱。

如需有關 ARMClient 命令的詳細資訊，請參閱這[文件](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>使用 Azure Resource Manager 用戶端來刪除復原服務保存庫

1. 執行下列命令，使用您的訂用帳戶識別碼、 資源群組名稱和保存庫名稱。 當您執行命令時，如果您沒有任何相依性會刪除保存庫。

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 如果保存庫不是空的您會收到錯誤 「 因為有此保存庫內的現有資源，無法刪除保存庫 」。 若要移除受保護的項目 / 容器內的保存庫中，執行下列動作：

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 入口網站中，確認已刪除保存庫。


## <a name="next-steps"></a>後續步驟

[深入了解](backup-azure-recovery-services-vault-overview.md)復原服務保存庫。<br/>
[深入了解](backup-azure-manage-windows-server.md)監視和管理復原服務保存庫。
