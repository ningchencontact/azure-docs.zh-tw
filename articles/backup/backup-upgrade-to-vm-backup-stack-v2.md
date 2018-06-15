---
title: 升級至 Azure VM 備份堆疊的 Azure Resource Manager 部署模型
description: VM 備份堆疊、Azure Resource Manager 部署模型的升級流程與常見問題集
services: backup, virtual-machines
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: e822e0c354fd671ee2802506e0e268d4078b395e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606897"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>升級至 Azure VM 備份堆疊的 Azure Resource Manager 部署模型
升級至虛擬機器 (VM) 備份堆疊的 Resource Manager 部署模型提供了下列增強功能：
* 能夠查看在備份作業中所建立的快照集 (可用於復原)，而不需等到資料轉送完成。 如此可減少觸發還原之前，將快照集複製到保存庫的等候時間。 此外，也能免去備份進階 VM 時的額外儲存需求 (第一次備份除外)。  

* 將快照集於本機上保存 7 天，以減少備份與還原時間。

* 支援最多 4 TB 磁碟大小。

* 還原時，可以使用非受控 VM 的原始儲存體帳戶。 即使 VM 的磁碟分散於多個儲存體帳戶，仍然具有此功能。 如此可讓還原在各種不同的 VM 設定下更加快速。
    > [!NOTE] 
    > 此功能不同於覆寫原始 VM。 
    >

## <a name="whats-changing-in-the-new-stack"></a>新堆疊有何改變？
目前的備份作業包含兩個階段：
1.  建立 VM 快照集。 
2.  將 VM 快照集傳輸到 Azure 備份保存庫。 

復原點只會在第 1 和第 2 階段完成之後才建立。 在新堆疊中，快照集一完成就會建立復原點。 您也可以使用相同的還原流程，由此復原點來還原。 您可以在 Azure 入口網站中，使用「快照集」復原點類型來識別此復原點。 快照集傳輸至保存庫後，復原點類型會變更為「快照集與保存庫」。 

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 儲存體與保存庫](./media/backup-azure-vms/instant-rp-flow.jpg) 

根據預設，快照集會保存 7 天。 此功能可以讓來自這些快照集的還原作業更快完成。 它可以減少將資料從保存庫複製到客戶儲存體帳戶所需的時間。 

## <a name="considerations-before-upgrade"></a>升級前的考量
* VM 備份堆疊為單向升級。 因此，所有備份都會進入此流程。 因為是在訂用帳戶層級上啟用，所以所有 VM 都會進入此流程。 所有新增加的功能將會建置在相同堆疊之上。 未來的版本會推出在原則層級控制此功能的能力。

* 快照集會儲存在本機上，以利於建立復原點，並加快還原速度。 因此，在 7 天期間內，儲存體成本會隨著快照集而增減。

* 增量快照會儲存為分頁 Blob。 使用非受控磁碟的所有客戶，將必須為儲存在客戶本機儲存體帳戶中的快照集付費 7 天。 根據目前的定價模式，使用受控磁碟的客戶無須付費。

* 如果您從快照集的復原點來還原進階 VM，便會看到在還原過程中建立 VM 時使用了暫時儲存位置。

* 對於進階儲存體帳戶，為立即復原所建立的快照集會佔用 10 TB 的配置空間。

## <a name="upgrade"></a>升級
### <a name="the-azure-portal"></a>Azure 入口網站
如果您使用 Azure 入口網站，將會在保存庫儀表板上看到通知。 此通知乃是關於大型磁碟支援，以及備份和還原速度的改進。

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 支援通知](./media/backup-azure-vms/instant-rp-banner.png) 

若要開啟畫面以升級至新的堆疊，請選取橫幅。 

![VM 備份堆疊 Resource Manager 部署模型的備份作業 -- 升級](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
從提高權限的 PowerShell 終端機執行下列 Cmdlet：
1.  登入您的 Azure 帳戶： 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  選取您想要註冊預覽的訂用帳戶：

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  註冊此訂用帳戶以使用私人預覽：

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>驗證已完成升級
從提高權限的 PowerShell 終端機執行下列 Cmdlet︰

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

如果顯示 "Registered" (已註冊)，則代表您的訂用帳戶已升級至 VM 備份堆疊 Resource Manager 部署模型。
