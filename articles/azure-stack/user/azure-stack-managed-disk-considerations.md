---
title: Azure Stack 中受控磁碟的差異與考量 | Microsoft Docs
description: 使用 Azure Stack 中的受控磁碟時，瞭解有關差異和注意事項。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 05efa7eaf6d95cbf63efd17b00d321d8c8509f28
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246774"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack 受控磁碟：差異與注意事項

本文摘要說明 [Azure Stack 受控磁碟](azure-stack-manage-vm-disks.md)和 [Azure 受控磁碟](../../virtual-machines/windows/managed-disks-overview.md)之間的已知差異。 若要深入了解 Azure Stack 與 Azure 之間的大致差異，請參閱[主要考量](azure-stack-considerations.md)文章。

受控磁碟會管理與 VM 磁碟相關的[儲存體帳戶](../azure-stack-manage-storage-accounts.md)，從而簡化 IaaS VM 的磁碟管理。

> [!Note]  
> Azure Stack 上的受控磁碟可從 1808 更新取得使用。 使用來自 1811 更新的 Azure Stack 入口網站來建立虛擬機器時，依預設會啟用此受控磁碟。
  

## <a name="cheat-sheet-managed-disk-differences"></a>速查表：受控磁碟的差異

| 功能 | Azure (全域) | Azure Stack |
| --- | --- | --- |
|待用資料的加密 |Azure 儲存體服務加密 (SSE)、Azure 磁碟加密 (ADE)     |BitLocker 128 位元 AES 加密      |
|映像          | 支援受控自訂映像 |尚不支援|
|備份選項 |支援 Azure 備份服務 |尚不支援 |
|災害復原選項 |支援 Azure Site Recovery |尚不支援|
|磁碟類型     |進階 SSD、標準 SSD (預覽) 和標準 HDD |進階 SSD、標準 HDD |
|進階磁碟  |完全支援 |可佈建，但無效能限制或保證  |
|進階磁碟 IOP  |取決於磁碟大小  |每一磁碟 2300 個 IOP |
|進階磁碟輸送量 |取決於磁碟大小 |每個磁碟 145 MB/秒 |
|磁碟大小  |Azure 進階磁碟：P4 (32 GiB) 至 P80 (32 TiB)<br>Azure 標準 SSD 磁碟：E10 (128 GiB) 至 E80 (32 TiB)<br>Azure 標準 HDD 磁碟：S4 (32 GiB) 至 S80 (32 TiB) |M4：32 GiB<br>M6：64 GiB<br>M10：128 GB<br>M15：256 GiB<br>M20：512 GB<br>M30：1024 GiB |
|磁碟快照複製|支援對連結至執行中 VM 的 Azure 受控磁碟進行快照|尚不支援 |
|磁碟效能分析 |彙總計量和每個磁碟支援的計量 |尚不支援 |
|移轉      |提供從現有非受控 Azure Resource Manager VM 進行移轉的工具，而不需要重新建立 VM  |尚不支援 |

> [!NOTE]  
> Azure Stack 中的受控磁碟 IOP 和輸送量是數量上限而不是佈建數量，可能會受到在 Azure Stack 中執行的硬體和工作負載影響。

## <a name="metrics"></a>度量

儲存體計量也有些差異：

- 使用 Azure Stack，儲存體度量中的交易資料並不區分內部或外部網路頻寬。
- 儲存體度量中的 Azure Stack 交易資料並不包含虛擬機器對所掛接磁碟的存取。

## <a name="api-versions"></a>API 版本

Azure Stack 受控磁碟支援下列 API 版本：

- 2017-03-30

## <a name="configuration"></a>組態

套用 1808 或更新版本的更新後，您必須先執行下列設定，再使用受控磁碟：

- 如果訂用帳戶是 1808 版更新之前建立的，請遵循下列步驟來更新訂用帳戶。 否則，在此訂用帳戶中部署 VM 可能會失敗，並出現錯誤訊息「磁碟管理員發生內部錯誤。」
   1. 在租用戶入口網站中，移至 [訂用帳戶] 並尋找訂用帳戶。 按一下 [資源提供者]，按一下 [Microsoft.Compute]，然後按一下 [重新註冊]。
   2. 在相同的訂用帳戶底下，移至 [存取控制 (IAM)]，並確認 [Azure Stack - 受控磁碟] 已列出。
- 如果您使用多個租用戶環境，請要求您的雲端操作員 (可以是您自己組織中的或來自服務提供者) 依照[本文](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的下列步驟重新設定每個來賓目錄。 否則，在與該來賓目錄相關聯的訂用帳戶中部署 VM 可能會失敗，並出現錯誤訊息「磁碟管理員發生內部錯誤。」


## <a name="next-steps"></a>後續步驟

- [了解 Azure Stack 虛擬機器](azure-stack-compute-overview.md)
