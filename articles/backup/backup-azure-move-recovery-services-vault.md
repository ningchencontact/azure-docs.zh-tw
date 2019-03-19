---
title: 跨 Azure 訂用帳戶或往另一個資源群組移動復原服務保存庫
description: 指導您如何跨 Azure 訂用帳戶和資源群組來移動復原服務保存庫。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2019
ms.author: sogup
ms.openlocfilehash: 0eb19ba8278df2d77466e5be13731723557e85a8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082070"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>跨 Azure 訂用帳戶和資源群組來移動復原服務保存庫 (有限公開預覽)

本文說明如何跨 Azure 訂用帳戶或往相同訂用帳戶中的另一個資源群組移動為 Azure 備份所設定的復原服務保存庫。 您可以使用 Azure 入口網站或 PowerShell 來移動復原服務保存庫。

## <a name="prerequisites-for-moving-a-vault"></a>移動保存庫的必要條件

- 在資源群組之間進行移動作業的期間，來源資源群組和目標資源群組均會遭到鎖定。 在移動作業完成之前，資源群組上會封鎖寫入和刪除作業。
- 只有訂用帳戶管理員才有權移動保存庫。
- 跨訂用帳戶移動保存庫時，目標訂用帳戶必須以啟用狀態存在，且必須位於與來源訂用帳戶相同的租用戶內。
- 您必須擁有在目標資源群組上執行寫入作業的權限。
- 您無法變更復原服務保存庫的位置。 移動保存庫只會變更資源群組。 新的資源群組可位於不同位置，但這種情況不會變更保存庫的位置。
- 目前每個區域一次可移動一個復原服務保存庫。
- 如果 VM 未隨著復原服務保存庫來跨訂用帳戶或往新的資源群組移動，則目前的 VM 復原點會在保存庫中維持不變直到過期。
- 無論 VM 是否隨著保存庫移動，您一律可以從保留在保存庫內的備份記錄還原 VM。
-   Azure 磁碟加密需要您的金鑰保存庫和 VM 位於相同的 Azure 區域和訂用帳戶中。
-   若要移動具有受控磁碟的虛擬機器，請參閱這篇[文章](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/)。
-   移動透過傳統模型所部署之資源的選項，會根據移動訂用帳戶內的資源還是將資源移到新的訂用帳戶而有所不同。 如需詳細資訊，請參閱這篇[文章](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations)。
-   在保存庫跨訂用帳戶或往新的資源群組移動之後，為保存庫所定義的備份原則會保留下來。
-   目前，您無法跨訂用帳戶和資源群組來移動在 IaaS VM 中含有 Azure 檔案儲存體、Azure 檔案同步或 SQL 的保存庫。 未來的版本會新增對於這些案例的支援。
-   如果您跨訂用帳戶移動含有 VM 備份資料的保存庫，則必須將 VM 移至相同的訂用帳戶，並使用相同的目標資源群組來繼續備份。<br>

> [!NOTE]
> 
> 依設定要與 **Azure Site Recovery** 搭配使用的復原服務保存庫還無法移動。 如果您已使用 **Azure Site Recovery** 來為任何 VM (Azure IaaS、Hyper-V、VMware) 或實體機器設定災害復原，則無法進行移動作業。 Site Recovery 服務的資源移動功能尚未正式運作。

## <a name="register-the-source-subscription-to-move-your-recovery-services-vault"></a>註冊來源訂用帳戶以移動復原服務保存庫

若要註冊來源訂用帳戶以**移動**復原服務保存庫，請從 PowerShell 終端機執行下列 Cmdlet：

1. 登入您的 Azure 帳戶

   ```
   Connect-AzureRmAccount
   ```

2. 選取您要註冊的訂用帳戶

   ```
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```
3. 註冊此訂用帳戶

   ```
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
   ```

4. 執行命令

   ```
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

等候 30 分鐘，讓訂用帳戶加入到允許清單，再使用 Azure 入口網站或 PowerShell 開始移動作業。

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>使用 Azure 入口網站將復原服務保存庫移動到不同的資源群組

將復原服務保存庫和其相關聯的資源移動到不同的資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟**復原服務保存庫**的清單，然後選取您想要移動的保存庫。 保存庫儀表板開啟時會如下圖所示。

   ![開啟復原服務保存庫](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   如果您沒看到保存庫的 [基本資訊] 資訊，請按下拉圖示。 您現在應該就會看到保存庫的 [基本資訊] 資訊。

   ![[基本資訊] 索引標籤](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保存庫的 [概觀] 功能表中，按一下 [資源群組] 旁的 [變更]，以開啟 [移動資源] 刀鋒視窗。

   ![變更資源群組](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. 在 [移動資源] 刀鋒視窗中，我們針對所選保存庫的建議是，藉由如下圖所示地選取核取方塊，來移動選擇性的相關資源。

   ![移動訂用帳戶](./media/backup-azure-move-recovery-services/move-resource.png)

5. 若要新增目標資源群組，請在 [資源群組] 下拉式清單中選取現有資源群組，或按一下 [建立新群組] 選項。

   ![建立資源](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 在新增資源群組之後，請確認 [我了解我必須先更新移動之資源所關聯的工具與指令碼，這些工具與指令碼才能使用新的資源識別碼] 選項，然後按一下 [確定] 以完成保存庫移動作業。

   ![確認訊息](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>使用 Azure 入口網站將復原服務保存庫移動到不同的訂用帳戶

您可以將復原服務保存庫和其相關聯的資源移動到不同的訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 開啟復原服務保存庫的清單，然後選取您想要移動的保存庫。 保存庫儀表板開啟時會如下圖所示。

    ![開啟復原服務保存庫](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    如果您沒看到保存庫的 [基本資訊] 資訊，請按下拉圖示。 您現在應該就會看到保存庫的 [基本資訊] 資訊。

    ![[基本資訊] 索引標籤](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保存庫的 [概觀] 功能表中，按一下 [訂用帳戶] 旁的 [變更]，以開啟 [移動資源] 刀鋒視窗。

   ![變更訂用帳戶](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 選取要移動的資源，建議您在這裡使用 [全選] 選項來選取所有列出的選擇性資源。

   ![移動資源](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 從 [訂用帳戶] 下拉式清單選取您想要作為保存庫移動目的地的目標訂用帳戶。
6. 若要新增目標資源群組，請在 [資源群組] 下拉式清單中選取現有資源群組，或按一下 [建立新群組] 選項。

   ![新增訂用帳戶](./media/backup-azure-move-recovery-services/add-subscription.png)

7. 按一下 [我了解我必須先更新移動之資源所關聯的工具與指令碼，這些工具與指令碼才能使用新的資源識別碼] 選項來進行確認，然後按一下 [確定]。

> [!NOTE]
> 不支援跨訂用帳戶備份 (RS 保存庫和受保護的 VM 位於不同訂用帳戶) 的案例。 此外，在保存庫移動作業進行期間，將無法修改從本機備援儲存體 (LRS) 到全域備援儲存體 (GRS) (反之亦然) 的儲存體備援選項。
>
>

## <a name="use-powershell-to-move-a-vault"></a>使用 PowerShell 來移動保存庫

若要將復原服務保存庫移動到另一個資源群組，請使用 `Move-AzureRMResource` Cmdlet。 `Move-AzureRMResource` 需要資源名稱和資源類型。 您可以從 `Get-AzureRmRecoveryServicesVault` Cmdlet 取得這兩項資訊。

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

若要將資源移到不同的訂用帳戶，請納入 `-DestinationSubscriptionId` 參數。

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

在執行上述 Cmdlet 之後，系統會要求您確認您想要移動指定的資源。 輸入 **Y** 來確認。 成功通過驗證後，資源便會移動。

## <a name="use-cli-to-move-a-vault"></a>使用 CLI 來移動保存庫

若要將復原服務保存庫移動到另一個資源群組，請使用下列 Cmdlet：

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

若要移動到新的訂用帳戶，請提供 `--destination-subscription-id` 參數。

## <a name="post-migration"></a>移轉後

1. 您需要設定/驗證資源群組的存取控制。  
2. 必須再次設定備份的報告和監視功能，保存庫的移動才會完成。 移動作業期間會失去先前的設定。



## <a name="next-steps"></a>後續步驟

您可以在資源群組和訂用帳戶之間移動許多不同類型的資源。

如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。
