---
title: "如何在 Azure Site Recovery 中將指令碼新增至復原方案 | Microsoft Docs"
description: "說明將新指令碼新增至「VMM 至 Azure」復原方案的先決條件"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>將 VMM 指令碼新增至復原方案


本文提供有關在 [Azure Site Recovery](site-recovery-overview.md) 中為復原方案建立和新增 VMM 指令碼的資訊。

在這篇文章下方或 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>將指令碼新增至復原方案的先決條件

在您的復原計畫中，您可以使用 PowerShell 指令碼。 您將需要撰寫指令碼並將其放在 VMM 程式庫中，才能從復原方案存取這些指令碼。 以下是您撰寫指令碼時的一些考量。

* 請確認指令碼使用的是 Try-Catch 區塊，以便能適當地處理例外狀況。
    - 如果在指令碼中出現例外狀況，指令碼會隨即停止執行，而工作也會顯示為失敗。
    - 一旦發生錯誤，指令碼的剩餘部分將不會執行。
    - 如果在執行未規劃的容錯移轉時發生錯誤，復原計畫將會繼續執行。
    - 如果在執行未規劃的容錯移轉時發生錯誤，復原計畫將會停止。 您必須修正指令碼，確認其能如預期執行，然後再次執行復原方案。
        - Write-Host 命令不會在復原方案指令碼中正常運作，指令碼將會失敗。 若要建立輸出，建立接著會執行主要指令碼的 proxy 指令碼。 請確定所有的輸出會使用 >> 命令經由管道輸出。
        - 如果指令碼未在 600 秒內傳回則會逾時。
        - 如果有任何資料寫入至 STDERR，則該指令碼將分類為失敗。 此資訊會顯示在指令碼的執行詳細資料中。

* 復原方案中的指令碼會依據 VMM 服務帳戶中的內容執行。 請確定此帳戶具有指令碼所在的遠端共用讀取權限。 測試指令碼執行 VMM 服務帳戶權限層級。
* 會在 Windows PowerShell 模組中傳遞 VMM Cmdlet。 安裝 VMM 主控台時，會一併安裝模組。 它可以使用下列指令碼中的命令載入到您的指令碼︰
   - Import-Module -Name virtualmachinemanager。 [深入了解](https://technet.microsoft.com/library/hh875013.aspx)。
* 確認您的 VMM 部署中至少有一部程式庫伺服器。 根據預設，VMM 伺服器的程式庫共用路徑位於本機的 VMM 伺服器上，資料夾名稱為 MSCVMMLibrary。
    * 如果您的程式庫共用路徑位於遠端 (或是位於本機上，但未與 MSCVMMLibrary 共用)，請依以下所示設為共用 (以 \\libserver2.contoso.com\share\ 為例)：
      * 開啟登錄編輯程式，並瀏覽至 **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**。
      * 編輯值 **ScriptLibraryPath**，並將其設為 \\libserver2.contoso.com\share\. 指定完整的 FQDN。 提供共用位置的權限。 請注意，這是共用的根節點。 **若要檢查此情形，您可以在 VMM 的根節點中瀏覽程式庫。開啟的路徑會是路徑的根目錄，就是您需要在變數中使用的路徑**。
      * 請確定您使用與 VMM 服務帳戶具有相同權限的使用者帳戶來測試指令碼。 這會檢查該獨立測試指令碼以它們在復原計畫中的相同方式執行。 在 VMM 伺服器上，設定要略過的執行原則，如下所示：
        * 以較高的權限開啟 **64 位元 Windows PowerShell** 主控台。
        * 類型： **Set-executionpolicy bypass**。 [深入了解](https://technet.microsoft.com/library/ee176961.aspx)。

> [!IMPORTANT]
> 您應該只在 64 位元 PowerShell 上，才將執行原則設為 Bypass (略過)。 如果您已針對 32 位元 PowerShell 做此設定，則指令碼將不會執行。

## <a name="add-the-script-to-the-vmm-library"></a>將指令碼新增至 VMM 程式庫

如果您有 VMM 來源站台，可在 VMM 伺服器上建立指令碼，並將其納入您的復原方案。

1. 在程式庫共用中建立新的資料夾。 例如，\<VMMServerName>\MSSCVMMLibrary\RPScripts。 將資料夾放在來源和目標 VMM 伺服器上。
2. 建立指令碼 (例如 RPScript)，並檢查其能否如預期運作。
3. 將指令碼放在來源和目標 VMM 伺服器的 \<VMMServerName>\MSSCVMMLibrary 位置。

## <a name="add-the-script-to-a-recovery-plan"></a>將指令碼新增至復原方案

您可以在將 VM 或複寫群組新增至群組之後，將指令碼新增至該群組，然後建立復原方案。

1. 開啟復原計畫。
2. 按一下 [步驟] 清單中的任意項目，然後按一下 [指令碼] 或 [手動動作]。
3. 指定要在已選取項目之前或之後新增指令碼或動作。 使用 [上移] 和 [下移] 按鈕，上下移動指令碼的位置。
4. 如果您新增 VMM 指令碼，請選取 [容錯移轉至 VMM 指令碼]。 在**指令碼路徑**中，輸入要共用的相對路徑。 在下列 VMM 範例中，您指定路徑︰**\RPScripts\RPScript.PS1**。
5. 如果新增 Azure 自動化 Runbook，請指定 Runbook 所在的 [Azure 自動化帳戶]，並選取適當的 [Azure Runbook 指令碼]。
6. 執行復原方案的測試容錯移轉，以確定指令碼如預期般運作。


## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-failover.md) 如何執行容錯移轉。
