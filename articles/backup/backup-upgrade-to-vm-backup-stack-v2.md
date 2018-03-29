---
title: 升級至 Azure VM 備份堆疊 V2 | Microsoft Docs
description: VM 備份堆疊 V2 的升級程序和常見問題集
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 6d214072bccb8b2b42828ee003dcf349985b4f43
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>升級至 VM 備份堆疊 V2
虛擬機器 (VM) 備份堆疊 V2 升級提供下列增強功能：
* 能夠查看在備份作業中所拍攝的快照集是否可用於復原，而不需等到資料轉送完成。
這可以縮短在觸發還原之前，等待快照集複製到保存庫的時間。 此外，這也會讓您免去備份進階 VM 時的額外儲存需求 (第一次備份除外)。  

* 藉由本機保留 7 天的快照集，可減少備份和還原次數。 

* 支援最多 4 TB 磁碟大小。  

* 執行非受控 VM 的還原時，能夠使用原始儲存體帳戶 (即使 VM 的磁碟分散在數個儲存體帳戶)。 這可讓還原在各種不同的 VM 設定下更加快速。 
    > [!NOTE] 
    > 這和覆寫原始 VM 不一樣。 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>新的堆疊有何改變？
現今的備份作業包含兩個階段：
1.  拍攝 VM 快照集。 
2.  將 VM 快照集傳輸到 Azure 備份保存庫。 

復原點只會在第 1 和第 2 階段完成之後才建立。 在新的堆疊中，快照集一完成就會建立復原點。 您也可以使用相同的還原流程由此復原點還原。 您可以在 Azure 入口網站中使用「快照集」復原點類型識別此復原點。 快照集傳輸至保存庫後，復原點類型會變更為「快照集與保存庫」。 

![VM 備份堆疊 V2 的備份作業](./media/backup-azure-vms/instant-rp-flow.jpg) 

根據預設，快照集會保留七天。 這使得從這些快照集還原能夠更快速完成，因為減少了將資料從保存庫複製回客戶儲存體帳戶所需的時間。 

## <a name="considerations-before-upgrade"></a>升級前的考量
* 這是 VM 備份堆疊的單向升級。 因此，未來所有的備份都會採取此流程。 因為是**在訂用帳戶層級啟用，所有 VM 都會採取此流程**。 所有新增加的功能將會建置在相同堆疊之上。 未來的版本會推出在原則層級控制此功能的能力。 
* 若 VM 使用進階磁碟，在第一次備份時，請確定直到第一次備份完成為止，儲存體帳戶中一直有 VM 大小的等量儲存空間可供使用。 
* 因為快照集儲存在本機 (為了幫助復原點建立及加速還原)，在這七天的期間，您會看到與快照集相對應的儲存體成本。
* 如果您從快照集的復原點還原進階 VM，您會看到在還原過程中建立 VM 時使用了暫時儲存位置。 

## <a name="how-to-upgrade"></a>如何升級？
### <a name="the-azure-portal"></a>Azure 入口網站
如果您使用 Azure 入口網站，在保存庫儀表板上會看到通知，敘述與大型磁碟支援以及備份和還原速度改善的資訊。

![VM 備份堆疊 V2 的備份作業](./media/backup-azure-vms/instant-rp-banner.png) 

若要開啟畫面以升級至新的堆疊，請選取橫幅。 

![VM 備份堆疊 V2 的備份作業](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
從提高權限的 PowerShell 終端機執行下列 Cmdlet︰
1.  登入您的 Azure 帳戶。 

```
PS C:> Login-AzureRmAccount
```

2.  選取您想要註冊預覽的訂用帳戶：

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  註冊此訂用帳戶以使用私人預覽：

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>確認升級是否完成
從提高權限的 PowerShell 終端機執行下列 Cmdlet︰

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

如果顯示 "Registered"，則您的訂用帳戶已升級至 VM 備份堆疊 V2。 



