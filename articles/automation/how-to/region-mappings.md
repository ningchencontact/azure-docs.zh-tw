---
title: Azure 自動化和 Log Analytics 工作區對應
description: 這篇文章說明支援解決方案允許自動化帳戶與 Log Analytics 工作區之間的對應
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8afe8fed8912dd365071f1c4c5560c9f5578dcd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66133107"
---
# <a name="workspace-mappings"></a>工作區對應

當啟用解決方案，例如更新管理、 變更追蹤和清查或啟動/停止 Vm 在離峰時間解決方案期間，只有特定區域支援連結的 Log Analytics 工作區和自動化帳戶。 此對應只適用於 「 自動化 」 帳戶和 Log Analytics 工作區。 回報給您的自動化帳戶或 Log Analytics 工作區的資源可位於其他區域。

## <a name="supported-mappings"></a>支援的對應

下表顯示支援的對應：

|**Log Analytics 工作區區域**|**Azure 自動化區域**|
|---|---|
|**美國**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canada**||
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

<sup>1</sup> EastUS 對應至自動化帳戶的 Log Analytics 工作區不完全的區域對應，而是正確的對應。

<sup>2</sup>因為容量限制的區域無法使用時建立新的資源。 這包括自動化帳戶和 Log Analytics 工作區。 不過，在區域中預先存在連結的資源應該繼續運作。

## <a name="unlink-workspace"></a>取消連結工作區

如果您決定您不再想要整合您的自動化帳戶與 Log Analytics 工作區，您可以取消連結您的帳戶，直接從 Azure 入口網站。 在繼續之前，必須先更新管理、 變更追蹤和清查或啟動/停止 Vm 移除在離峰時間解決方案期間，如果您使用它們。 如果您不會移除，此程序將無法繼續。 檢閱已匯入特定解決方案的相關文章，以了解移除解決方案所需的步驟。

移除這些解決方案之後，您可以執行下列步驟以將您的自動化帳戶取消連結。

> [!NOTE]
> 某些包含舊版 Azure SQL 監視解決方案的解決方案可能已建立自動化資產，在取消連結工作區之前，可能也需要先加以移除。

1. 從 Azure 入口網站開啟您的自動化帳戶，然後在 [自動化帳戶] 頁面上，在左側的 [相關資源]  區段下選取 [已取消連結的工作區]  。

2. 在 [取消連結工作區] 頁面上，按一下 [取消連結工作區]  。 您會收到提示，確認您想要繼續。

3. 當 Azure 自動化嘗試將您的帳戶從 Log Analytics 工作區取消連結時，您可以從功能表在 [通知]  下追蹤進度。

若使用「更新管理」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 更新排程 - 每個都會有符合您建立的更新部署名稱

* 針對解決方案建立的混合式背景工作角色群組 - 每個都會具有如下名稱：machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8。

若使用「於下班時間啟動/停止 VM」解決方案，您可以在移除解決方案之後選擇移除已不再需要的下列項目。

* 啟動及停止 VM Runbook 排程
* 啟動及停止 VM Runbook
* 變數

或者，您也可以取消連結您的工作區從您的自動化帳戶從您的 Log Analytics 工作區。 在您的工作區中，選取**自動化帳戶**下方**相關資源**。 在 [自動化帳戶] 頁面上選取**取消連結帳戶**。

## <a name="next-steps"></a>後續步驟

了解如何上架下列解決方案：

更新管理 」 和 「 變更追蹤和清查：

* 從[虛擬機器](../automation-onboard-solutions-from-vm.md)
* 從您[自動化帳戶](../automation-onboard-solutions-from-automation-account.md)
* 當[瀏覽多部電腦](../automation-onboard-solutions-from-browse.md)
* 從[runbook](../automation-onboard-solutions.md)

於下班時間開始/停止 VM

* [在離峰時間部署啟動/停止 Vm](../automation-solution-vm-management.md)