---
title: 將 Azure 虛擬機器移至新的訂用帳戶或資源群組 |Microsoft Docs
description: 使用 Azure Resource Manager 將虛擬機器移至新的資源群組或訂用帳戶。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723541"
---
# <a name="move-guidance-for-virtual-machines"></a>移動虛擬機器指導方針

這篇文章描述目前不支援的案例和移動備份虛擬機器的步驟。

## <a name="scenarios-not-supported"></a>不支援的案例

尚未支援下列案例：

* 受控的磁碟位於可用性區域中無法移到不同的訂用帳戶中。
* 憑證儲存在 Key Vault 中的虛擬機器可以移動至相同訂用帳戶中的新資源群組，但是無法跨訂用帳戶移動。
* 不能移動標準 SKU 負載平衡器或標準 SKU 公用 IP 的虛擬機器擴展集。
* 從 Marketplace 資源建立且附加方案的虛擬機器無法在資源群組或訂用帳戶之間移動。 在目前的訂用帳戶中取消佈建虛擬機器，然後於新訂用帳戶中再次部署。
* 在現有的虛擬網路，但您的虛擬機器不虛擬網路中移動的所有資源。

## <a name="virtual-machines-with-azure-backup"></a>使用 Azure 備份的虛擬機器

若要移動使用 Azure 備份設定的虛擬機器，請使用下列因應措施：

* 尋找您虛擬機器的位置。
* 尋找具有下列命名模式的資源群組：`AzureBackupRG_<location of your VM>_1`，例如 AzureBackupRG_westus2_1
* 如果是在 Azure 入口網站中，則請選取 [顯示隱藏的類型]
* 如果是在 PowerShell 中，請使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` Cmdlet
* 如果是在 CLI 中，請使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 尋找類型為 `Microsoft.Compute/restorePointCollections` 且命名模式為 `AzureBackup_<name of your VM that you're trying to move>_###########` 的資源
* 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。
* 完成刪除之後，您可以將保存庫和虛擬機器移至的目標訂用帳戶。 移動之後，您可以繼續備份而不會遺失資料。
* 如需移動復原服務保存庫以進行備份的相關資訊，請參閱[復原服務限制](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。
