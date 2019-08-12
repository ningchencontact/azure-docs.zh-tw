---
title: 將 Azure Site Recovery 設定移到另一個 Azure 區域 | Microsoft Docs
description: 用於將 Site Recovery 設定移到另一個 Azure 區域的指引
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708461"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>將復原服務保存庫和 Azure Site Recovery 設定移到另一個 Azure 區域

在許多情況下，您可能會想要將現有的 Azure 資源從某個區域移到另一個區域，例如可管理性、治理原因或因為公司合併/收購等。當您移動 Azure VM 時，您可能會想要移動的其中一個相關資源是相同的災害復原設定。 沒有任何最佳方式可將現有的災害復原設定從某個區域移到另一個區域。 基本上，這是因為您已根據來源 VM 區域設定目標區域，因此當您決定做出變更時，目標區域的既有設定將無法重複使用而必須重設。 本文會定義可重新設定災害復原設定並移至不同區域的逐步程序。

在本文件中，您將會：

> [!div class="checklist"]
> * 驗證移動的先決條件
> * 識別 Azure Site Recovery 所使用的資源 
> * 停用複寫
> * 刪除資源 
> * 根據 VM 的新來源區域重新設定站台復原。

> [!IMPORTANT]
> 目前沒有最佳方式可將復原服務保存庫和 DR 設定原封不動地移至不同區域，本文件會引導客戶完成停用複寫並在新區域中加以重設的程序。

## <a name="prerequisites"></a>必要條件

- 請先確定您已移除並刪除災害復原設定，再嘗試將 Azure VM 移至不同區域。 

> [!NOTE]
> 如果 Azure VM 的新目標區域與災害復原的目標區域相同，則可以使用現有的複寫設定，並根據[此處](azure-to-azure-tutorial-migrate.md)所述步驟進行移動 

- 請確定您會做出明智決策，並通知專案關係人，因為 VM 在移動完成之前將不會受到災害防護。 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>識別 Azure Site Recovery 所使用的資源
我們會建議您先執行此步驟，再繼續進行下一步，因為在 VM 仍會複寫時比較容易識別相關的資源

針對所要複寫的每個 Azure VM，請瀏覽至 [受保護的項目]   > [複寫的項目]  >[屬性]  並識別下列資源

- 目標資源群組
- 快取儲存體帳戶
- 目標儲存體帳戶 (如果是非受控磁碟型 Azure VM) 
- 目標網路


## <a name="disable-existing-disaster-recovery-configuration"></a>停用現有的災害復原設定

1. 瀏覽至 [復原服務保存庫]  
2.  在 [受保護的項目]   > [已複寫的項目]  中，以滑鼠右鍵按一下機器 > [停用複寫]  。
3. 針對您想要移動的所有 VM 重複此步驟。
> [!NOTE]
> 行動服務將無法從受保護的伺服器解除安裝，您必須手動解除安裝。 如果您打算再次保護伺服器，可以略過解除安裝行動服務。

## <a name="delete-the-resources"></a>刪除資源

1. 瀏覽至 [復原服務保存庫] 
2. 按一下 [刪除] 
3. 繼續刪除[上一個步驟](#identify-the-resources-that-were-used-by-azure-site-recovery)中所識別的所有其他資源
 
## <a name="move-azure-vms-to-the-new-target-region"></a>將 Azure VM 移至新的目標區域

根據您的需求遵循下列步驟，將 Azure VM 移至目標區域。

- [將 Azure VM 移至其他區域](azure-to-azure-tutorial-migrate.md)
- [將 Azure VM 移至可用性區域中](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>根據 VM 的新來源區域重新設定站台復原

使用[此處](azure-to-azure-tutorial-enable-replication.md)所述的步驟，為已移至新區域的 Azure VM 設定災害復原
