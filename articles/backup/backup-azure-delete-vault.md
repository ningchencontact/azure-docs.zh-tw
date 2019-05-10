---
title: 删除 Azure 中的恢复服务保管库
description: 介绍如何删除恢复服务保管库。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: raynew
ms.openlocfilehash: e7cea725a25d48ac9f1ffad6acc434e21145890e
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473252"
---
# <a name="delete-a-recovery-services-vault"></a>刪除復原服務保存庫

本文介绍如何删除 [Azure 备份](backup-overview.md)恢复服务保管库。 其中分别说明了如何删除依赖项，以及如何删除保管库和强制删除保管库。


## <a name="before-you-start"></a>開始之前

在开始之前必须知道，无法删除注册了服务器的，或者存有备份数据的恢复服务保管库。

- 若要正常删除某个保管库，请取消注册其中的服务器、删除保管库数据，然后删除该保管库。
- 如果你尝试删除一个仍具有依赖项的保管库，则系统会发出错误消息。 需要手动删除保管库的依赖项，包括：
    - 备份的项
    - 受保护的服务器
    - 备份管理服务器（Azure 备份服务器、DPM）![选择保管库以打开其仪表板](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- 如果你不想要保留恢复服务保管库中的任何数据，并想要删除此保管库，可以强制删除它。
- 如果您嘗試刪除保存庫，但是無法刪除，則保存庫仍會設定為接收備份資料。


## <a name="delete-a-vault-from-the-azure-portal"></a>在 Azure 门户中删除保管库

1. 開啟保存庫儀表板。  
2. 在仪表板中单击“删除”。 确认是否要删除。

    ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

如果收到错误，请依次删除[备份项](#remove-backup-items)、[基础结构服务器](#remove-backup-infrastructure-servers)、[恢复点](#remove-azure-backup-agent-recovery-points)和保管库。

![删除保管库错误](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>刪除復原服務保存庫使用 Azure Resource Manager 用戶端

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 安裝從 chocolatey[此處](https://chocolatey.org/)並安裝 ARMClient 執行下列命令：

   ` choco install armclient --source=https://chocolatey.org/api/v2/ `
2. 登入 Azure 帳戶，執行下列命令

    ` ARMClient.exe login [environment name] `

3. 在 Azure 门户中，收集所要删除的保管库的订阅 ID 和资源组名称。

如需有關 ARMClient 命令的詳細資訊，請參閱這[文件](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>使用 Azure Resource Manager 用戶端來刪除復原服務保存庫

1. 執行下列命令，使用您的訂用帳戶識別碼、 資源群組名稱和保存庫名稱。 W\hen 您執行命令，它會刪除保存庫，如果您沒有任何相依性。

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 如果该保管库不是空的，则会出现错误“由于此保管库中存在现有资源，因此无法将其删除”。 若要移除受保護的項目 / 容器內的保存庫中，執行下列動作：

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 门户中，确认要删除该保管库。


## <a name="remove-vault-items-and-delete-the-vault"></a>删除保管库项并删除保管库

这些过程通过一些示例来说明如何删除备份数据和基础结构服务器。 删除保管库中的所有内容后，可以删除该保管库。

### <a name="remove-backup-items"></a>删除备份项

此过程通过一个示例说明如何从 Azure 文件中删除备份数据。

1. 单击“备份项” > “Azure 存储(Azure 文件)”

    ![選取備份類型](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. 右键单击要删除的每个 Azure 文件项，然后单击“停止备份”。

    ![選取備份類型](./media/backup-azure-delete-vault/stop-backup-item.png)


3. 在“停止备份” > “选择选项”中，选择“删除备份数据”。
4. 键入项的名称，然后单击“停止备份”。
   - 这表示你确认要删除该项。
   - 确认后，“停止备份”按钮将会激活。
   - 如果保留（而不是删除）该数据，便无法删除保管库。

     ![刪除備份資料](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. （可选）提供删除数据的原因，并添加备注。
6. 若要确认删除作业是否已完成，请检查 Azure 消息。 ![刪除備份資料](./media/backup-azure-delete-vault/messages.png)上也提供本文中使用的原始碼。
7. 该作业完成后，服务会发送以下消息：“备份过程已停止，备份数据已删除”。
8. 刪除清單中的項目後，在 [備份項目] 功能表上按一下 [重新整理]，以查看保存庫中的項目。

      ![刪除備份資料](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>删除备份基础结构服务器

1. 在保管库仪表板菜单中，单击“备份基础结构”。
2. 单击“备份管理服务器”以查看服务器。

    ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 右键单击该项并选择“删除”。
4. 若要确认删除作业是否已完成，请检查 Azure 消息。 ![刪除備份資料](./media/backup-azure-delete-vault/messages.png)上也提供本文中使用的原始碼。
5. 该作业完成后，服务会发送以下消息：“备份过程已停止，备份数据已删除”。
6. 刪除項目在清單中，在後**備份基礎結構**功能表上，按一下**重新整理**查看保存庫中的項目。


### <a name="remove-azure-backup-agent-recovery-points"></a>删除 Azure 备份代理恢复点

1. 在保管库仪表板菜单中，单击“备份基础结构”。
2. 单击“备份管理服务器”以查看基础结构服务器。

    ![選取您的保存庫以開啟其儀表板](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. 在 [受保護的伺服器] 清單中，按一下 [Azure 備份代理程式]。

    ![選取備份類型](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. 在使用 Azure 备份代理保护的服务器列表中单击该服务器。

    ![選取特定受保護的伺服器](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 在选定服务器的仪表板上，单击“删除”。

    ![刪除選取的伺服器](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. 在 [刪除] 功能表中，輸入項目的名稱，然後按一下 [刪除]。

     ![刪除備份資料](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. （可选）提供删除数据的原因，并添加备注。
8. 若要确认删除作业是否已完成，请检查 Azure 消息。 ![刪除備份資料](./media/backup-azure-delete-vault/messages.png)上也提供本文中使用的原始碼。
9. 刪除項目在清單中，在後**備份基礎結構**功能表上，按一下**重新整理**查看保存庫中的項目。

### <a name="delete-the-vault-after-removing-dependencies"></a>删除依赖项后删除保管库

1. 删除所有依赖项后，在保管库菜单中滚动到“概要”窗格。
2. 确认是否未列出任何“备份项”、“备份管理服务器”或“复制的项”。 如果保管库中仍有项，请删除这些项。

3. 當保存庫中沒有其他項目時，在保存庫儀表板中按一下 [刪除] 。

    ![刪除備份資料](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 若要確認您想要刪除保存庫，請按一下 [是]。 保存庫便會被刪除，入口網站會回到 [新增]  服務功能表。

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>如果我停止備份程序但卻保留了資料，會怎麼樣？

如果停止备份过程，但意外保留了数据，则必须先按前面的部分中所述删除备份数据。

## <a name="next-steps"></a>後續步驟

[了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
