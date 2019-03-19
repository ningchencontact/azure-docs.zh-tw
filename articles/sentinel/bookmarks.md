---
title: 追蹤的資料時追捕 Sentinel 使用追捕書籤之預覽版的 Azure |Microsoft Docs
description: 本文說明如何使用 Azure Sentinel 追捕書籤來追蹤的資料。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 9a7ceeab6d2ad761752f778038692256bd87624b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242831"
---
# <a name="keep-track-of-data-during-hunting"></a>追蹤的資料期間追捕

> [!IMPORTANT]
> Azure 的 Sentinel 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
威脅追捕通常會需要檢閱山脈尋求的惡意行為的辨識項的記錄資料。 在此過程中，pointkeith 尋找他們想要記住、 重新瀏覽及分析的驗證可能假設，並了解完整的資訊洩漏的一部分的事件。
追捕書籤，幫助您這樣做，請藉由保留已在 Log Analytics 中執行以及您認定執行相關的查詢結果的查詢。 您也可以記錄您內容的觀察值，並參考您的發現，加上附註和標記。 會顯示您和小組成員輕鬆共同作業加上書籤的資料。   

您可以重新瀏覽您加上書籤的資料隨時都在**書籤**索引標籤**追捕**頁面。 您可以使用篩選，然後搜尋您目前的調查，快速找到特定資料的選項。 或者，您可以在其中檢視您加上書籤的資料直接在**HuntingBookmark** Log Analytics 中的資料表。 這可讓您篩選、 彙總，以及聯結加上書籤的資料與其他資料來源，方便您尋找 corroborating 辨識項。

您也可以視覺化您加上書籤的資料，依序按一下**調查**。 這會啟動在其中檢視、 調查及以視覺化方式進行通訊使用互動式的實體 graph 圖表] 和 [時間軸所發現的調查體驗。


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>從 Azure Sentinel 執行 Log Analytics 查詢

1. 在 Azure Sentinel 入口網站中，按一下**追捕**執行可疑和異常行為的查詢。

1. 若要執行追捕行銷活動，選取其中一個追捕查詢然後在左側，檢閱結果。 

1. 按一下 **檢視查詢結果**追捕查詢中**詳細資料**Log Analytics 中的頁面，即可檢視查詢結果。 以下是您看到您已執行自訂的 SSH 暴力密碼破解攻擊查詢的範例。
  
   ![顯示結果](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>加入書籤

1. 在 Log Analytics 查詢結果清單中，展開包含您感興趣的資訊的資料列。

4. 選取省略符號 （...），結尾的資料列，然後選取**加入追捕書籤**。
5. 在右側，在**詳細資料**頁面上，更新名稱，然後新增標記，以及可協助您識別哪些項目為感興趣項目的相關資訊。
6. 按一下 **儲存**來認可變更。 所有加上書籤的資料與其他 investigator，共用且共同作業的調查體驗的第一步。

   ![顯示結果](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> 您也可以使用任意啟動從 Azure Sentinel Log Analytics 記錄檔 頁面上或查詢飛從 Log Analytics 頁面並開啟從追捕 頁面上建立的 Log Analytics 查詢中使用書籤。 您無法加入書籤，如果您啟動從外部 Azure Sentinel 的 Log Analytics。 

## <a name="view-and-update-bookmarks"></a>檢視和更新的書籤 

1. 在 Azure Sentinel 入口網站中，按一下**追捕**。 
2. 按一下 [**書籤**頁面中間的檢視清單中的書籤] 索引標籤。
3. 您可以使用搜尋 方塊或篩選選項來尋找特定書籤。
4. 在下方方格中選取個別的書籤，右手邊的 [詳細資料] 窗格中檢視書籤詳細資料。
5. 若要更新標籤和資訊，請在按一下可編輯的文字方塊，然後按一下 **儲存**以保留您的變更。

   ![顯示結果](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>在 Log Analytics 中檢視已標記書籤的資料 

有多個選項，可在 Log Analytics 中檢視您加上書籤的資料。 

若要檢視已標記書籤的查詢、 結果或歷程記錄最簡單的方式是選取在所需的書籤**書籤**資料表，並使用詳細資料窗格中提供的連結。 選項包括： 
- 按一下 **檢視查詢**Log Analytics 中檢視來源查詢。  
- 按一下 **檢視書籤記錄**若要查看所有書籤的中繼資料包括： 哪些人進行了更新、 更新的值，並更新發生的時間。 

- 您也可以檢視所有書籤的未經處理的書籤資料上即可**書籤記錄**書籤中方格上方。 此檢視會顯示您所有書籤追捕書籤資料表相關聯的中繼資料中。 您可以使用 KQL 查詢來篩選到特定的書籤，您所需的最新版本。  


> [!NOTE]
> 建立書籤，以及它會顯示在之間可以有明顯的延遲 （以分鐘為單位） **HuntingBookmark**資料表。 建議您先建立您的書籤，然後會擷取資料之後加以分析。 

## <a name="delete-a-bookmark"></a>刪除書籤
如果您想要刪除書籤，請執行下列： 
1.  開啟 [th**追捕書籤**] 索引標籤。 
2.  選取的目標書籤。
3.  選取省略符號 （...），結尾的資料列，然後選取**刪除書籤**。
    
刪除書籤時，會移除清單中的書籤**書籤** 索引標籤。Log Analytics"HuntingBookmark"資料表包含前一個書籤項目，將會繼續，但最新的項目會變更**SoftDelete**值設為 true，讓您輕鬆地篩選掉舊的書籤。  正在刪除書籤不會移除任何實體與其他的書籤或警示相關聯的調查體驗。 


## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何執行中 Azure Sentinel 使用書籤追捕調查。 若要深入了解 Azure Sentinel，請參閱下列文章：


- [主動積極的威脅](hunting.md)
- [使用 notebook 來執行自動化的追捕行銷活動](notebooks.md)