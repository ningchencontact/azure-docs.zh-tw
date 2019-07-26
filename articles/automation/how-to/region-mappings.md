---
title: Azure 自動化和 Log Analytics 工作區對應
description: 本文說明自動化帳戶與 Log Analytics 工作區之間允許的對應, 以支援解決方案
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd4500b4ef6492f0b6499bb1e9aa1a773313e860
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498375"
---
# <a name="workspace-mappings"></a>工作區對應

啟用更新管理、變更追蹤和清查等解決方案, 或在離峰期間啟動/停止 Vm 解決方案時, 只有特定區域支援連結 Log Analytics 工作區和自動化帳戶。 此對應僅適用于自動化帳戶和 Log Analytics 工作區。 向您的自動化帳戶或 Log Analytics 工作區報告的資源可位於其他區域。

## <a name="supported-mappings"></a>支援的對應

下表顯示支援的對應：

|**Log Analytics 工作區區域**|**Azure 自動化區域**|
|---|---|
|**美國**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**加拿大**||
|CanadaCentral|CanadaCentral|
|**亞太地區**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**歐洲**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> Log Analytics 工作區與自動化帳戶的 EastUS 對應不是區域對應的確切區域, 而是正確的對應。

<sup>2</sup>由於容量限制, 建立新資源時無法使用區域。 這包括自動化帳戶和 Log Analytics 工作區。 不過, 區域中預先存在的連結資源應該會繼續工作。

## <a name="unlink-workspace"></a>取消連結工作區

如果您決定不想再整合您的自動化帳戶與 Log Analytics 工作區, 您可以直接從 Azure 入口網站取消連結您的帳戶。 在繼續之前, 您必須先移除更新管理、變更追蹤和清查, 或在離峰時間解決方案中啟動/停止 Vm (如果您使用它們)。 如果您未移除這些專案, 將無法繼續執行此程式。 檢閱已匯入特定解決方案的相關文章，以了解移除解決方案所需的步驟。

移除這些解決方案之後，您可以執行下列步驟以將您的自動化帳戶取消連結。

> [!NOTE]
> 某些包含舊版 Azure SQL 監視解決方案的解決方案可能已建立自動化資產，在取消連結工作區之前，可能也需要先加以移除。

1. 從 Azure 入口網站開啟您的自動化帳戶，然後在 [自動化帳戶] 頁面上，在左側的 [相關資源] 區段下選取 [已取消連結的工作區]。

2. 在 [取消連結工作區] 頁面上，按一下 [取消連結工作區]。 您會收到提示, 確認您想要繼續。

3. 當 Azure 自動化嘗試將您的帳戶從 Log Analytics 工作區取消連結時，您可以從功能表在 [通知] 下追蹤進度。

若使用「更新管理」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 更新排程 - 每個都會有符合您建立的更新部署名稱

* 為解決方案建立的混合式背景工作角色群組, 每個都會`machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`以類似的方式命名。

若使用「於下班時間啟動/停止 VM」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 啟動及停止 VM Runbook 排程
* 啟動及停止 VM Runbook
* 變數

或者, 您也可以從 Log Analytics 工作區, 將您的工作區從您的自動化帳戶取消連結。 在您的工作區中, 選取 [**相關資源**] 下的 [**自動化帳戶**]。 在 [自動化帳戶] 頁面上, 選取 [**取消連結帳戶**]。

## <a name="next-steps"></a>後續步驟

瞭解如何上架下列解決方案:

更新管理和變更追蹤和清查:

* 從[虛擬機器](../automation-onboard-solutions-from-vm.md)
* 從您的[自動化帳戶](../automation-onboard-solutions-from-automation-account.md)
* [流覽多部電腦](../automation-onboard-solutions-from-browse.md)時
* 從[runbook](../automation-onboard-solutions.md)

於下班時間開始/停止 VM

* [在下班時間部署啟動/停止 Vm](../automation-solution-vm-management.md)
