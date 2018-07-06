---
title: 從 Azure VM 讓更新管理、變更追蹤和清查解決方案上線
description: 了解如何在 Azure 虛擬機器上讓屬於 Azure 自動化一部分的更新管理、變更追蹤和清查解決方案上線。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f270b2ccea51e83bc6475051b8667bf73d7fd717
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221507"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>從 Azure 虛擬機器讓更新管理、變更追蹤和清查解決方案上線

Azure 自動化提供的解決方案可協助您管理作業系統安全性更新、追蹤變更，以及清查您的電腦上安裝的項目。 讓機器上線的方法很多。 您可以從虛擬機器、[從自動化帳戶](automation-onboard-solutions-from-automation-account.md)、[透過瀏覽多部機器](automation-onboard-solutions-from-browse.md)或藉由使用 [Runbook](automation-onboard-solutions.md) 來讓解決方案上線。 本文說明如何從 Azure 虛擬機器讓這些解決方案上線。

## <a name="sign-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="enable-the-solutions"></a>啟用解決方案

移至現有的虛擬機器。 在 [作業] 底下，選取 [更新管理]、[清查] 或 [變更追蹤]。

若只要啟用 VM 的解決方案，請確定您已選取 [對此 VM 啟用]。 若要在解決方案中將多部機器上線，請選取 [對此訂用帳戶中的 VM 啟用]，然後選取 [按一下以選取要啟用的機器]。 若要了解如何讓多部機器一次上線，請參閱[讓更新管理、變更追蹤和清查解決方案上線](automation-onboard-solutions-from-automation-account.md)。

選取 Azure Log Analytics 工作區與自動化帳戶，然後選取 [啟用] 來啟用解決方案。 啟用解決方案最多需要 15 分鐘。

![讓更新管理解決方案上線](media/automation-onboard-solutions-from-vm/onboard-solution.png)

移至其他解決方案，然後選取 [啟用]。 Log Analytics 和自動化帳戶下拉式清單會停用，原因是這些解決方案使用的工作區和自動化帳戶與先前啟用的解決方案相同。

> [!NOTE]
> [變更追蹤] 和 [清查] 使用相同的解決方案。 當其中的某個解決方案啟用時，另一個也會啟用。

## <a name="scope-configuration"></a>範圍設定

每個解決方案都會使用工作區中的範圍設定，來鎖定會取得解決方案的電腦。 範圍設定是一或多個已儲存搜尋的群組，用以將解決方案的範圍限定於特定電腦。 若要存取自動化帳戶中的範圍設定，請在 [相關資源] 之下，選取 [工作區]。 在工作區的 [工作區資料來源] 底下，選取 [範圍設定]。

如果選取的工作區還沒有 [更新管理] 或 [變更追蹤] 解決方案，則系統會建立以下範圍設定：

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

如果選取的工作區已有解決方案，則不會重新部署解決方案，也不會新增範圍設定。

選取任何設定上的省略符號 (**...**)，然後選取 [編輯]。 在 [編輯範圍設定] 窗格中選取 [選取電腦群組]。 [電腦群組] 窗格會顯示用來建立範圍設定的已儲存搜尋。

## <a name="saved-searches"></a>已儲存的搜尋

當電腦新增至「更新管理」、「變更追蹤」或「清查」解決方案時，系統會將電腦新增至工作區中兩個已儲存搜尋的其中一個。 這兩個已儲存的搜尋是包含這些解決方案所鎖定電腦的查詢。

移至工作區。 在 [一般] 下，選取 [已儲存的搜尋]。 下表會顯示這些解決方案所使用的兩個已儲存搜尋：

|Name     |類別  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

選取任一個已儲存的搜尋，以檢視用來填入群組的查詢。 下圖顯示查詢與其結果：

![已儲存的搜尋](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>後續步驟

繼續進行解決方案的教學課程以了解其使用方式：

* [教學課程 - 管理 VM 的更新](automation-tutorial-update-management.md)
* [教學課程 - 識別 VM 上的軟體](automation-tutorial-installed-software.md)
* [教學課程 - 對 VM 的變更進行疑難排解](automation-tutorial-troubleshoot-changes.md)
