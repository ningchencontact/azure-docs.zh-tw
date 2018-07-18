---
title: 新增 Azure 虛擬機器的擴充計量 | Microsoft Docs
description: 這篇文章可協助您啟用及設定 Azure VM 的擴充診斷計量。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 58245478cf49c030c435b487e233bbc893a2b9a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296351"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>新增 Azure 虛擬機器的擴充計量

成本管理會使用 Azure VM 的 Azure 計量資料來顯示其資源的詳細資訊。 計量資料也稱為效能計數器，可讓成本管理用於產生報告。 不過，成本管理不會自動收集來賓 VM 的所有 Azure 計量資料，您必須啟用計量收集。 這篇文章可協助您啟用及設定 Azure VM 的其他診斷計量。

啟用計量集合之後，您可以：

- 知道 VM 何時達到其記憶體、磁碟和 CPU 限制。
- 偵測使用趨勢和異常情況。
- 根據使用量調整大小以控制您的成本。
- 從成本管理取得有效調整成本大小的最佳化建議。

例如，您可以監視 Azure VM 的 CPU % 和記憶體 %。 Azure VM 計量會對應至「[主機] 百分比 CPU」和「[來賓] 記憶體百分比」。

## <a name="verify-that-metrics-are-enabled-on-vms"></a>確認 VM 上已啟用計量

1. 在 http://portal.azure.com 登入 Azure 入口網站。
2. 在 [虛擬機器] 下選取 VM，然後在 [監視] 下選取 [計量]。 此時會顯示可用計量清單。
3. 選取某些計量，隨即出現顯示資料的圖形。  
    ![計量範例 - 主機百分比 CPU](./media/azure-vm-extended-metrics/metric01.png)

在上述範例中，有一組有限的標準計量可供您的主機使用，但沒有記憶體計量可用。 記憶體計量是擴充計量的一部分。 您必須執行一些額外的步驟，才能啟用擴充計量。 下列資訊將引導您啟用它們。

## <a name="enable-extended-metrics-in-the-azure-portal"></a>在 Azure 入口網站中啟用擴充計量

標準計量是主機電腦計量。 「[主機] 百分比 CPU」計量是一個例子。 來賓 VM 也有一些基本計量，它們也稱為擴充計量。 擴充計量的範例包括「[來賓] 記憶體百分比」和「[來賓] 可用記憶體」。

啟用擴充計量相當簡單。 針對每個 VM 啟用來賓層級監視。 當您啟用來賓層級的監視時，Azure 診斷代理程式就會安裝在 VM 上。 下列程序對傳統和一般 VM 而言一樣，而對於 Windows 和 Linux VM 而言也一樣。

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>在現有的 VM 上啟用來賓層級監視

1. 在 [虛擬機器] 上，檢視您的 VM 清單，然後選取 VM。
2. 在 [監視] 下，選取 [計量]。
3. 按一下 [診斷設定]。
4. 在 [診斷設定] 頁面中，按一下 [啟用來賓層級監視]。 Linux VM 需要現有的儲存體帳戶。 若未選擇 Windows VM 的儲存體帳戶，則會為您建立一個。  
    ![啟用來賓層級監視](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. 幾分鐘後，Azure 診斷代理程式就會安裝在 VM 上。 重新整理頁面，系統會以來賓計量更新可用計量清單。  
    ![擴充計量](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>在新 VM 上啟用來賓層級監視

建立新 VM 時，確定選取 [來賓 OS 診斷]。 Linux VM 需要現有的儲存體帳戶。 若未選擇 Windows VM 的儲存體帳戶，則會為您建立一個。

![啟用來賓 OS 診斷](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Resource Manager 認證

啟用擴充計量後，請確定成本管理可存取您的 [Resource Manager 認證](activate-subs-accounts.md)。 成本管理需要您的認證，才能收集和顯示您的 VM 效能資料。 這些認證也會用來建立成本最佳化建議。 成本管理至少需要執行個體中三天的效能資料，才能判斷它是否為縮減建議的候選項目。

## <a name="enable-vm-metrics-with-a-script"></a>使用指令碼啟用 VM 計量

您可以使用 Azure PowerShell 指令碼來啟用 VM 計量。 當您想要在許多 VM 上啟用計量時，您可以使用指令碼來自動執行此程序。 範例指令碼位於 GitHub 上的 [Azure 啟用診斷](https://github.com/Cloudyn/azure-enable-diagnostics)。

## <a name="view-azure-performance-metrics"></a>檢視 Azure 效能計量

若要在 Cloudyn 入口網站中檢視 Azure 執行個體的效能計量，請瀏覽至 [資產] > [計算] > [執行個體總管]。 在 VM 執行個體清單中，展開執行個體，然後展開資源以檢視詳細資料。

![執行個體總管](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>後續步驟

- 若尚未針對您的帳戶啟用 Azure Resource Manager API 存取權，請繼續進行[啟動 Azure 訂用帳戶與帳戶](activate-subs-accounts.md)
