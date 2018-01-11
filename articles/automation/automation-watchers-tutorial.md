---
title: "在 Azure 自動化帳戶中建立監看員工作 | Microsoft Docs"
description: "了解如何在 Azure 自動化帳戶中建立監看員工作，以監看在資料夾中建立的新檔案。"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: eamono
ms.openlocfilehash: 294faa48f9840919b087594835706bad8048d45b
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>建立 Azure 自動化監看員工作，以追蹤在本機電腦上的檔案變更

Azure 自動化會使用監看員工作監看事件和觸發的動作。 本教學課程會帶領您建立新的檔案新增至目錄時所要監視的監看員工作。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 匯入 runbook 監看員
> * 建立自動化變數
> * 建立動作的 runbook
> * 建立監看員工作
> * 觸發程序監看員
> * 檢查輸出

## <a name="prerequisites"></a>必要條件

若要完成本教學課程中，需要下列條件：

* Azure 訂用帳戶。 如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自動化帳戶](automation-offering-get-started.md)以保存監看員和動作 Runbook，以及監看員工作。
* 執行監看員工作的[混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md)。

## <a name="import-a-watcher-runbook"></a>匯入 runbook 監看員

本教學課程會使用監看員 runbook 呼叫**監看式 NewFile**尋找目錄中的新檔案。 監看員 runbook 擷取資料夾中檔案的上次已知的寫入時間，並查看該浮水印比更新的任何檔案。 在此步驟中，您匯入此 runbook 自動化帳戶。

1. 開啟您的自動化帳戶，然後按一下**Runbook**頁面。
1. 按一下**瀏覽圖庫** 按鈕。
1. 「 監看員 runbook 」，選取搜尋**尋找目錄中的新檔案的監看員 runbook**選取**匯入**。
  ![從 UI 匯入自動化 runbook](media/automation-watchers-tutorial/importsourcewatcher.png)
1. 授與 runbook 名稱、 描述和選取**確定**將 runbook 匯入您的自動化帳戶。
1. 選取**編輯**，然後按一下 **發行**。 當系統提示您選取**是**發佈的 runbook。

## <a name="create-an-automation-variable"></a>建立自動化變數

[自動化變數](automation-variables.md)用來儲存上述 runbook 讀取，並從每個檔案所儲存的時間戳記。 

1. 選取**變數**下**共用資源**選取**+ 加入變數**。
1. 請輸入"監看式 NewFileTimestamp 」 名稱
1. 選取日期時間類型。
1. 按一下**建立** 按鈕。 這會建立的自動化變數。

## <a name="create-an-action-runbook"></a>建立動作的 runbook

動作 runbook 中監看員工作用於處理資料傳遞給它，從監看員 runbook。 在此步驟中，您可以更新匯入預先定義的動作 runbook 呼叫 」 程序 NewFile"。

1. 瀏覽至您的自動化帳戶，然後選取**Runbook**下**程序自動化**類別目錄。
1. 按一下**瀏覽圖庫** 按鈕。
1. 「 監看員動作 」 搜尋並選取**監看員 runbook 所觸發的事件處理的監看員動作**選取**匯入**。
  ![從 UI 匯入動作 runbook](media/automation-watchers-tutorial/importsourceaction.png)
1. 授與 runbook 名稱、 描述和選取**確定**將 runbook 匯入您的自動化帳戶。
1. 選取**編輯**，然後按一下 **發行**。 當系統提示您選取**是**發佈的 runbook。

## <a name="create-a-watcher-task"></a>建立監看員工作

監看員工作包含兩個部分。 監看員和動作。 監看員會監看員工作中定義的間隔執行。 從監看員 runbook 的資料會傳遞至動作 runbook。 在此步驟中，您可以設定參考上述步驟中所定義的監看員與動作 runbook 監看員工作。

1. 瀏覽至您的自動化帳戶，然後選取**監看員工作**下**程序自動化**類別目錄。
1. 選取 監看員工作 頁面，然後按一下**+ 加入監看員工作** 按鈕。
1. 請輸入"WatchMyFolder"做為名稱。

1. 選取**設定監看員**選取**監看式 NewFile** runbook。

1. 為各個參數輸入下列值︰

   * **FOLDERPATH** -混合式背景工作上取得建立新檔案的資料夾。 d:\examplefiles
   * **延伸模組**-保留空白可處理所有副檔名。
   * **RECURSE** -保留為預設值，這個值。
   * **回合設定**-挑選混合式背景工作。

1. 按一下 [確定]，然後按一下 [選取] 以返回監看員頁面。
1. 選取**設定動作**選取 「 處理程序 NewFile 」 runbook。
1. 針對參數輸入下列值︰

   *    **EVENTDATA** -保留空白。 資料會從監看員 Runbook 傳入。  
   *    **回合設定**-保留為 Azure 自動化服務中執行此 runbook。

1. 按一下**確定**，然後選取以返回 監看員頁面。
1. 按一下**確定**建立監看員工作。

![設定從 UI 監看員動作](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>觸發程序監看員

若要測試監看員會如預期般運作，您需要建立測試檔案。

遠端登入的混合式背景工作。 開啟**PowerShell**並建立測試檔案的資料夾中。
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

下列範例顯示預期的輸出。

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>檢查輸出

1. 瀏覽至您的自動化帳戶，然後選取**監看員工作**下**程序自動化**類別目錄。
1. 選取的監看員工作 」 WatchMyFolder"。
1. 按一下**檢視監看員的資料流**下**資料流**監看員找到新的檔案，而且啟動動作 runbook。
1. 若要查看執行 runbook 作業，請按一下**檢視監看員動作工作**。 每個工作可以是選取檢視作業詳細資料。

   ![從 UI 監看員動作工作](media/automation-watchers-tutorial/WatcherActionJobs.png)

下列範例中，就可以看到找到新的檔案時的預期的輸出：

```
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 匯入 runbook 監看員
> * 建立自動化變數
> * 建立動作的 runbook
> * 建立監看員工作
> * 觸發程序監看員
> * 檢查輸出

跟隨此連結以深入了解撰寫您自己的 runbook。

> [!div class="nextstepaction"]
> [我的第一個 PowerShell runbook](automation-first-runbook-textual-powershell.md)。
