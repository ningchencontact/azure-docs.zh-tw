---
title: 了解如何從 Azure 虛擬機器登入更新管理、變更追蹤和清查解決方案
description: 了解如何在 Azure 虛擬機器上登入屬於 Azure 自動化一部分的更新管理、變更追蹤和清查解決方案
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 675aebf35a6bee6e4cc4fd884204edb5bae4b848
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830556"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>從 Azure 虛擬機器登入更新管理、變更追蹤和清查解決方案

Azure 自動化提供的解決方案可管理作業系統安全性更新、追蹤變更，以及清查您的電腦上安裝的項目。 讓機器上架的方式有很多種，您可以從虛擬機器、[從您的自動化帳戶](automation-onboard-solutions-from-automation-account.md)、[從瀏覽多部機器](automation-onboard-solutions-from-browse.md)，或透過 [Runbook](automation-onboard-solutions.md) 來讓解決方案上架。 本文說明如何從 Azure 虛擬機器登入這些解決方案。

## <a name="log-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 登入 Azure

## <a name="enable-the-solutions"></a>啟用解決方案

瀏覽至現有的虛擬機器，並選取 [作業] 下的 [更新管理]、[清查] 或 [變更追蹤]。

若要啟用 VM 的解決方案，只須確認已選取 [對此 VM 啟用] 選項按鈕，若要將多部機器上架至解決方案，請選取 [對此訂用帳戶中的 VM 啟用]，然後按一下 [按一下以選取要啟用的機器]。 請參閱[上架更新管理、變更追蹤和清查解決方案](automation-onboard-solutions-from-automation-account.md)，以檢視一次上架多部機器的步驟。

選擇 Log Analytics 工作區和自動化帳戶，然後按一下 [啟用] 以啟用解決方案。 啟用解決方案最多需要 15 分鐘。

![使更新解決方案上線](media/automation-onboard-solutions-from-vm/onboard-solution.png)

瀏覽至其他解決方案，然後按一下 [啟用]，記錄分析和自動化帳戶下拉式方塊會停用，因為它們使用的工作區和自動化帳戶與先前啟用的解決方案相同。

> [!NOTE]
> [變更追蹤] 和 [清查] 使用相同的解決方案，因此啟用其中之一時，另一個也會啟用。

## <a name="scope-configuration"></a>範圍設定

每個解決方案都會使用工作區中的範圍設定，來設定取得解決方案的電腦。 範圍設定是一或多個已儲存搜尋的群組，用以將解決方案的範圍限定於特定電腦。 若要存取範圍設定，請在您自動化帳戶中的 [相關資源] 下選取 [工作區]，然後在工作區的 [工作區資料來源] 下選取 [範圍設定]。

如果選取的工作區還沒有 [更新管理] 或 [變更追蹤] 解決方案，則系統會建立以下範圍設定：

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

如果選取的工作區已經有該解決方案。 系統不會重新部署解決方案，範圍設定也不會新增至解決方案。

按一下任何設定上的省略符號 (...)，然後選取 [編輯]。 在 [編輯範圍設定] 頁面上選取 [選取電腦群組]，以開啟 [電腦群組] 頁面。 此頁面會顯示用來建立範圍設定的已儲存搜尋。

## <a name="saved-searches"></a>已儲存的搜尋

當電腦新增至「更新管理」或「變更追蹤」和「清查」解決方案時，它們會被新增至工作區中兩個已儲存搜尋的其中一個。 這些已儲存的搜尋都是查詢，其中包含這些解決方案的目標電腦。

瀏覽至您的工作區，並選取 [一般] 下的 [已儲存的搜尋]。 下表顯示這些解決方案所使用的兩個已儲存搜尋：

|Name     |類別  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

選取任一個已儲存的搜尋，檢視用來填入群組的查詢。 下圖顯示查詢及其結果。

![已儲存的搜尋](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>後續步驟

繼續進行解決方案的教學課程以了解如何使用。

* [教學課程 - 管理 VM 的更新](automation-tutorial-update-management.md)

* [教學課程 - 識別 VM 上的軟體](automation-tutorial-installed-software.md)

* [教學課程 - 對 VM 的變更進行疑難排解](automation-tutorial-troubleshoot-changes.md)
