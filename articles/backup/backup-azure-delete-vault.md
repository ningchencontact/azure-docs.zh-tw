---
title: 在 Azure 中刪除復原服務保存庫
description: 說明如何刪除復原服務保存庫。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 9c63170b60a871182042acab8a35e505c603f260
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018878"
---
# <a name="delete-a-recovery-services-vault"></a>刪除復原服務保存庫

本文說明如何刪除[Azure 備份](backup-overview.md)復原服務保存庫。 其中包含移除相依性, 然後刪除保存庫的指示。


## <a name="before-you-start"></a>開始之前

您無法刪除具有相依性的復原服務保存庫, 例如受保護的伺服器或與保存庫相關聯的備份管理伺服器。

- 無法刪除包含備份資料的保存庫 (也就是, 即使您已停止保護, 但仍保留備份資料)。

- 如果您刪除包含相依性的保存庫, 則會顯示下列錯誤:

  ![刪除保存庫錯誤](./media/backup-azure-delete-vault/error.png)

- 如果您從包含相依性的入口網站刪除內部部署受保護專案 (MARS、MABS 或 DPM 至 Azure), 則會顯示警告訊息:

  ![刪除受保護的伺服器錯誤](./media/backup-azure-delete-vault/error-message.jpg)

  
若要正常地刪除保存庫, 請選擇符合您設定的案例, 並遵循建議的步驟:

狀況 | 移除相依性以刪除保存庫的步驟 |
-- | --
我有使用 Azure 備份代理程式 (MARS) 備份至 Azure 的內部部署檔案和資料夾 | 執行刪除備份資料和備份專案中的步驟-[適用于 MARS 代理程式](#delete-backup-items-from-mars-management-console)
我有使用 MABS (Microsoft Azure 備份 Server) 或 DPM (System Center Data Protection Manager) 保護的內部部署機器 | 執行刪除備份資料和備份專案- [MABS 代理程式](#delete-backup-items-from-mabs-management-console)中的步驟
我在雲端中有受保護的專案 (例如 laaS VM、Azure 檔案共用等)  | 執行 [刪除備份資料和備份專案] 中的步驟-[針對雲端中的受保護專案](#delete-protected-items-in-cloud)
我在內部部署和雲端都有受保護的專案 | 依照下列循序執行刪除備份資料和備份專案中的步驟: <br> - [針對雲端中的受保護專案](#delete-protected-items-in-cloud)<br> - [針對 MARS 代理程式](#delete-backup-items-from-mars-management-console) <br> - [針對 MABS 代理程式](#delete-backup-items-from-mabs-management-console)
我在內部部署或雲端沒有任何受保護的專案;不過, 我仍在取得保存庫刪除錯誤 | [使用 Azure Resource Manager 用戶端執行刪除復原服務保存庫](#delete-the-recovery-services-vault-using-azure-resource-manager-client)中的步驟


## <a name="delete-protected-items-in-cloud"></a>刪除雲端中受保護的專案

繼續閱讀 **[本](#before-you-start)** 節之前, 請先瞭解相依性和保存庫刪除程式。

若要停止保護並刪除備份資料, 請執行下列步驟:

1. 從入口網站 > 復原**服務保存庫** > **備份專案**, 選擇雲端中的受保護專案 (例如 AzureVirtual 機器、Azure 儲存體 (Azure 檔案儲存體)、SQL on Azure VM 等等)。

    ![選取備份類型](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 以滑鼠右鍵按一下備份專案。 根據備份專案是否受保護而定, 功能表將顯示 [**停止備份**] 或 [**刪除備份資料**]。

    - 針對 [**停止備份**], 從下拉式選單選取 [**刪除備份資料**]。 輸入備份專案的**名稱**(區分大小寫), 選取 [**原因**], 輸入 [**批註**], 然後按一下 [**停止備份**]。

        ![選取備份類型](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 針對 [**刪除備份資料**], 輸入備份專案的名稱 (區分大小寫), 選取 [**原因**], 輸入 [**批註**], 然後按一下 [**刪除**]。 

         ![刪除備份資料](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 檢查 [刪除備份資料](./media/backup-azure-delete-vault/messages.png)]**通知** ![。 完成後, 服務會顯示訊息:**正在停止備份並刪除「*備份專案*」的備份資料**。 **已成功完成**作業。
6. 按一下 [**備份專案**] 功能表上的 [重新整理], 以檢查備份專案是否已移除。

      ![刪除備份資料](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>刪除內部部署的受保護專案

繼續閱讀 **[本](#before-you-start)** 節之前,請先瞭解相依性和保存庫刪除程式。

1. 從保存庫的 [儀表板] 功能表, 按一下 [**備份基礎結構**]。
2. 視您的內部部署案例而定, 選擇下列選項:

      - 針對**Azure 備份代理程式**, 請選擇 [**受保護的伺服器** >  **Azure 備份代理程式**], 然後選取您想要刪除的伺服器。 

        ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - 針對**Azure 備份伺服器**/ **DPM**, 請選擇 [ **備份管理伺服器**]。 選取您想要刪除的伺服器。 


          ![選取保存庫以開啟其儀表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. [**刪除**] 分頁隨即出現, 並顯示警告訊息。

     ![刪除備份資料](./media/backup-azure-delete-vault/delete-protected-server.png)

     檢查警告訊息和 [同意] 核取方塊中提供的指示。
    
    > [!NOTE]
    >- 如果受保護的伺服器與 Azure 服務同步, 而且存在備份專案, 則 [同意] 核取方塊會顯示相依的備份專案數目, 以及用來查看備份專案的連結。
    >- 如果受保護的伺服器不會與 Azure 服務同步, 而且存在備份專案, 則 [同意] 核取方塊會顯示備份專案的數目。
    >- 如果備份專案不存在, 則 [同意] 核取方塊會要求刪除。

4. 選取 [同意] 核取方塊, 然後按一下 [**刪除**]。




5. 檢查 [刪除備份資料](./media/backup-azure-delete-vault/messages.png)]**通知** ![。 完成後, 服務會顯示訊息:**正在停止備份並刪除「*備份專案*」的備份資料**。 **已成功完成**作業。
6. 按一下 [**備份專案**] 功能表上的 [重新整理], 以檢查備份專案是否已移除。

您現在可以從管理主控台繼續刪除備份專案:
    
   - [使用 MARS 保護的專案](#delete-backup-items-from-mars-management-console)
    - [使用 MABS 保護的專案](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>從 MARS 管理主控台刪除備份專案

從 MARS 管理主控台刪除備份專案

- 啟動 MARS 管理主控台, 移至 [**動作**] 窗格, 然後選擇 [**排程備份**]。
- 從 [**修改或停止排程備份**嚮導] 中, 選擇 [**停止使用此備份排程並刪除所有儲存的備份**] 選項, 然後按 **[下一步]** 。

    ![修改或停止排定的備份](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- 在 [**停止排程的備份**嚮導] 中, 按一下 **[完成]** 。

    ![停止排程的備份](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- 系統會提示您輸入安全性 Pin 碼。 若要產生 PIN, 請執行下列步驟:
  - 登入 Azure 入口網站。
  - 瀏覽至 [復原服務保存庫] > [設定] > [屬性]。
  - 在 [安全性 PIN 碼] 底下，按一下 [產生]。 複製此 PIN。 (此 PIN 僅適用于五分鐘)。
- 在管理主控台 (用戶端應用程式) 中貼上 PIN, 然後按一下 **[確定]** 。

  ![安全性 Pin 碼](./media/backup-azure-delete-vault/security-pin.png)

- 在 [**修改備份進度**] 中, 您會*看到已刪除的備份資料將會保留14天。在這段時間之後, 備份資料將會永久刪除。*  

    ![刪除備份基礎結構](./media/backup-azure-delete-vault/deleted-backup-data.png)

現在您已從內部部署刪除備份專案, 請從入口網站完成後續步驟。

### <a name="delete-backup-items-from-mabs-management-console"></a>從 MABS 管理主控台刪除備份專案

從 MABS 管理主控台刪除備份專案

**方法 1**若要停止保護並刪除備份資料, 請執行下列步驟:

1.  在 DPM 管理主控台中, 按一下巡覽列上的 [**保護**]。
2.  在 [顯示] 窗格中, 選取您要移除的保護群組成員。 以滑鼠右鍵按一下以選擇 [**停止保護群組成員**] 選項。
3.  從 [**停止保護**] 對話方塊中, 選取 [**刪除受保護的資料** > ] [**線上刪除存放裝置**] 核取方塊, 然後按一下 [**停止保護**]。

    ![線上刪除儲存體](./media/backup-azure-delete-vault/delete-storage-online.png)

受保護的成員狀態現在已變更為**可用的非**作用中複本。

4. 以滑鼠右鍵按一下非作用中的保護群組, 然後選取 [**移除非**作用中保護]。

    ![移除非作用中保護](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. 從 [**刪除非**作用中保護] 視窗選取 [**刪除線上存放裝置**], 然後按一下 **[確定]** 。

    ![移除磁片上的複本並上線](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**方法 2**啟動**MABS 管理**主控台。 在 [**選擇資料保護方式**] 區段中, 取消選取 [**我想要線上保護**]。

  ![選取資料保護方式](./media/backup-azure-delete-vault/data-protection-method.png)

現在您已從內部部署刪除備份專案, 請從入口網站完成後續步驟。


## <a name="delete-the-recovery-services-vault"></a>刪除復原服務保存庫

1. 移除所有相依性之後, 請在 [保存庫] 功能表中, 移至 [**基本**] 窗格。
2. 確認沒有任何 [**備份專案**]、[**備份管理伺服器**] 或 [複寫的**專案] 已**列出。 如果專案仍然出現在保存庫中, 請參閱[開始之前](#before-you-start)一節。

3. 當保存庫中沒有其他項目時，在保存庫儀表板中按一下 [刪除]。

    ![刪除備份資料](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 若要確認您想要刪除保存庫，請按一下 [是]。 保存庫便會被刪除，入口網站會回到 [新增] 服務功能表。

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>使用 Azure Resource Manager 用戶端刪除復原服務保存庫

只有當移除所有相依性, 而且您仍然收到保存*庫刪除錯誤*時, 才建議使用此選項來刪除復原服務保存庫。

- 從保存庫功能表的 [**基本**] 窗格中, 確認沒有任何 [**備份專案**]、[**備份管理伺服器**] 或 [複寫的**專案] 已**列出。 如果有備份專案, 請參閱[開始之前](#before-you-start)一節。
- 再次嘗試[從入口網站刪除保存庫](#delete-the-recovery-services-vault)。
- 如果移除所有相依性, 但您仍收到保存*庫刪除錯誤*, 請使用 ARMClient 工具來執行下列步驟:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 從[這裡](https://chocolatey.org/)安裝 chocolatey, 並安裝 ARMClient 執行下列命令:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登入您的 Azure 帳戶, 然後執行此命令:

    `ARMClient.exe login [environment name]`

3. 在 Azure 入口網站中, 收集您想要刪除之保存庫的訂用帳戶識別碼和資源組名。

如需 ARMClient 命令的詳細資訊, 請參閱這[份檔](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>使用 Azure Resource Manager 用戶端來刪除復原服務保存庫

1. 使用您的訂用帳戶識別碼、資源組名和保存庫名稱來執行下列命令。 當您執行命令時, 如果您沒有任何相依性, 它會刪除保存庫。

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 如果保存庫不是空的, 您將會收到錯誤「因為此保存庫中有現有的資源, 所以無法刪除保存庫」。 若要移除保存庫中受保護的專案/容器, 請執行下列動作:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 入口網站中, 確認已刪除保存庫。


## <a name="next-steps"></a>後續步驟

[深入瞭解](backup-azure-recovery-services-vault-overview.md)復原服務保存庫。<br/>
[瞭解如何](backup-azure-manage-windows-server.md)監視和管理復原服務保存庫。
