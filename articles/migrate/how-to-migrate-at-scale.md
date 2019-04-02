---
title: 自動將大量 VM 移轉至 Azure | Microsoft Docs
description: 描述如何使用 Azure Site Recovery 以指令碼遷移大量 VM
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: f90140e9464ee72e9ceae8ca140bd060c51aade8
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762645"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>使用 Azure Site Recovery 大規模移轉 VM

本文會協助您了解使用 Azure Site Recovery 以指令碼遷移大量 VM 的程序。 您可以在 GitHub 上的 [Azure PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery)存放庫下載這些指令碼。 指令碼可用來將 VMware、 AWS、 GCP Vm 和實體伺服器移轉至 Azure 和支援的移轉至受控磁碟。 如果您以實體伺服器的形式來遷移 VM，您也可以使用這些程式碼來遷移 Hyper-V VM。 指令碼會利用[這裡](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell)記載的 Azure Site Recovery PowerShell。

## <a name="current-limitations"></a>目前的限制：
- 支援指定靜態 IP 位址僅適用於目標 VM 的主要 NIC
- 指令碼不會採用 Azure Hybrid Benefit 相關輸入，您需要在入口網站中手動更新複寫 VM 的屬性

## <a name="how-does-it-work"></a>運作方式

### <a name="prerequisites"></a>必要條件
開始之前，您需要執行下列步驟：
- 確定 Site Recovery 保存庫是在 Azure 訂用帳戶中建立
- 確定組態伺服器和處理序伺服器安裝在來源環境，且保存庫可以探索環境
- 確定複寫原則已建立且與組態伺服器建立關聯
- 確認您已新增 VM 系統管理員帳戶的組態伺服器 （用以複寫在內部部署 Vm）
- 確定已建立在 Azure 中的目標成品
    - 目標資源群組
    - 目標儲存體帳戶 （和其資源群組）-建立進階儲存體帳戶如果您打算移轉至進階受控磁碟
    - 快取儲存體帳戶 （和其資源群組）-在 保存庫位於相同區域中建立標準儲存體帳戶
    - 用於容錯移轉的虛擬網路 (和其資源群組)
    - 目標子網路
    - 用於測試容錯移轉的虛擬網路 (和其資源群組)
    - 可用性設定組 (若需要)
    - 目標網路安全性群組和其資源群組
- 確定您已決定目標 VM 的內容
    - 目標 VM 名稱
    - Azure 中的目標 VM 大小 (可以使用 Azure Migrate 評量來決定)
    - VM 中主要 NIC 的私人 IP 位址
- 從 GitHub 上的 [Azure PowerShell 範例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery)存放庫下載指令碼

### <a name="csv-input-file"></a>CSV 輸入檔案
完成所有必要條件之後，您需要建立具有資料的每個您想要移轉的來源機器的 CSV 檔案。 輸入 CSV 必須有標頭行 (具有輸入詳細資料)，以及資料列 (具有必須遷移的每一部機器的詳細資料)。 所有指令碼是設計成在相同的 CSV 檔案上運作。 範例 CSV 範本可以在指令碼資料夾中取得，供您參考。

### <a name="script-execution"></a>指令碼執行
CSV 準備就緒之後，您可以執行下列步驟來執行內部部署 VM 的移轉：

**步驟 #** | **指令碼名稱** | **說明**
--- | --- | ---
1 | asr_startmigration.ps1 | 針對 CSV 中列出的所有 VM 啟用複寫，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料
2 | asr_replicationstatus.ps1 | 檢查複寫的狀態，指令碼會建立 CSV，其中具有每部 VM 的狀態
3 | asr_updateproperties.ps1 | VM 複寫/受保護之後，使用此指令碼來更新 VM 的目標屬性 (Compute 和 Network 屬性)
4 | asr_propertiescheck.ps1 | 確認屬性是否已適當更新
5 | asr_testmigration.ps1 |  對 CSV 中列出的 VM 啟動測試容錯移轉，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料
6 | asr_cleanuptestmigration.ps1 | 一旦您手動驗證 VM 已進行測試容錯移轉，您可以使用此指令碼來清除測試容錯移轉 VM
7 | asr_migration.ps1 | 對 CSV 中列出的 VM 執行非計劃性容錯移轉，指令碼會建立 CSV 輸出，其中具有每部 VM 的作業詳細資料。 指令碼不會關閉內部部署 Vm 之前觸發容錯移轉的應用程式一致性，建議您以手動方式關閉 Vm 執行指令碼之前。
8 | asr_completemigration.ps1 | 執行的 Vm 上的認可作業，並刪除 Azure Site Recovery 實體
9 | asr_postmigration.ps1 | 如果您打算將網路安全性群組指派給 NIC 容錯移轉後，您可以使用此指令碼來完成這項操作。 它會將 NSG 指派給目標 VM 中的任何一個 NIC。

## <a name="how-to-migrate-to-managed-disks"></a>如何移轉至受控磁碟？
指令碼，根據預設，移轉 Vm 至 Azure 中的受控磁碟。 如果提供的目標儲存體帳戶是進階儲存體帳戶，進階受控磁碟會建立 post 移轉。 快取儲存體帳戶仍然可以是標準帳戶。 如果目標儲存體帳戶是標準儲存體帳戶，在移轉頁面時，會建立標準磁碟。 

## <a name="next-steps"></a>後續步驟

[深入了解](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)使用 Azure Site Recovery 將伺服器遷移至 Azure
