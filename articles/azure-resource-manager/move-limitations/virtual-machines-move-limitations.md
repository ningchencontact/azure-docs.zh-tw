---
title: 將 Azure 虛擬機器移至新的訂用帳戶或資源群組 |Microsoft Docs
description: 使用 Azure Resource Manager 將虛擬機器移至新的資源群組或訂用帳戶。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tomfitz
ms.openlocfilehash: 443d6f2bcbb61d9106b079a4e63c48bb433d19c6
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286729"
---
# <a name="move-guidance-for-virtual-machines"></a>適用于虛擬機器的移動指引

本文說明目前不支援的案例，以及使用備份來移動虛擬機器的步驟。

## <a name="scenarios-not-supported"></a>不支援的案例

尚未支援下列案例：

* 可用性區域中的受控磁碟無法移至不同的訂用帳戶。
* 不能移動標準 SKU Load Balancer 或標準 SKU 公用 IP 的虛擬機器擴展集。
* 從 Marketplace 資源建立且附加方案的虛擬機器無法在資源群組或訂用帳戶之間移動。 取消布建目前訂用帳戶中的虛擬機器，然後在新的訂用帳戶中再次部署。
* 當您不移動虛擬網路中的所有資源時，無法將現有虛擬網路中的虛擬機器移至新的訂用帳戶。
* 低優先順序的虛擬機器和低優先順序的虛擬機器擴展集無法在資源群組或訂用帳戶之間移動。
* 無法個別移動可用性設定組中的虛擬機器。

## <a name="virtual-machines-with-azure-backup"></a>具有 Azure 備份的虛擬機器

若要移動使用 Azure 備份設定的虛擬機器，請使用下列因應措施：

* 尋找您虛擬機器的位置。
* 尋找具有下列命名模式的資源群組：`AzureBackupRG_<location of your VM>_1`，例如 AzureBackupRG_westus2_1
* 如果是在 Azure 入口網站中，則請選取 [顯示隱藏的類型]
* 如果是在 PowerShell 中，請使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` Cmdlet
* 如果是在 CLI 中，請使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 尋找類型為 `Microsoft.Compute/restorePointCollections` 且命名模式為 `AzureBackup_<name of your VM that you're trying to move>_###########` 的資源
* 刪除此資源。 此作業只會刪除立即復原點，而不會刪除保存庫中備份的資料。
* 刪除完成後，您可以將保存庫和虛擬機器移至目標訂用帳戶。 移動之後，您可以繼續備份而不會遺失資料。
* 如需移動復原服務保存庫以進行備份的相關資訊，請參閱[復原服務限制](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。
