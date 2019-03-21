---
title: Azure Sentinel 預覽版中的追捕功能 |Microsoft Docs
description: 本文說明如何使用 Azure Sentinel 追捕功能。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: adedc8bc1f574ae089f2a11033fab4f390c57a9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087018"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>搜尋具有 Azure Sentinel 預覽版中的威脅

> [!IMPORTANT]
> Azure 的 Sentinel 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果您想要積極主動地尋找是否有安全性威脅，Azure Sentinel 強大追捕搜尋和查詢工具，來搜尋是否有安全性威脅，跨組織的資料來源的調查。 但您的系統和安全性設備產生山脈的可能很難剖析，並篩選成有意義的事件的資料。 若要協助安全性分析師尋找主動新的異常狀況未偵測到的安全性應用程式，Azure Sentinel' 內建追捕查詢引導您詢問適當的問題，您已經在您的網路的資料中發現的問題。 

比方說，一個內建的查詢會提供您基礎結構上執行最常見的處理序的相關資料-您不希望他們執行每個時間相關警示，可能是完全無害，但您可能想要看看在某些情況下，看看是否查詢 there 的不尋常的任何項目。 



使用 Azure Sentinel 追捕，您可以利用下列功能：

- 內建的查詢：若要開始，開始的頁面會提供預先載入的查詢範例，協助您開始，並協助您熟悉的資料表和查詢語言。 這些內建追捕查詢開發的 Microsoft 安全性研究人員上持續執行，新增新的查詢，並為您提供進入點來尋找新的偵測，並找出要從何處開始四處尋找 設定現有的微調查詢新的攻擊的開頭。 

- 具有 IntelliSense 的功能強大的查詢語言：為建置基礎的查詢語言，可讓您的彈性，您必須對追捕上一層樓。

- 建立您自己的書籤：追捕過程中，您可能會遇到相符項目或結果、 儀表板或尋找異常或可疑的活動。 若要將標示這些項目，因此您可以回到它們在未來，使用書籤功能。 書籤可讓您將項目供稍後使用，儲存到用來建立調查的案例。 如需有關書籤的詳細資訊，請參閱使用 [追捕中的書籤]。

- 使用 notebook 來自動化調查：Notebook 就像是逐步的劇本，您可以建置，來逐步進行的調查步驟和搜尋。  Notebook 會將封裝中的可重複使用的劇本可以與其他人共用您的組織中的所有追捕步驟。 
- 查詢儲存的資料：若要查詢資料表中存取資料。 例如，您可以查詢建立的程序、 DNS 事件和其他許多事件型別。

- 社群連結：利用更大的社群能夠找到其他的查詢和資料來源。
 
## <a name="get-started-hunting"></a>開始使用捕捉

1. 在 Azure Sentinel 入口網站中，按一下**追捕**。
  ![Azure 的 Sentinel 啟動追捕](media/tutorial-hunting/hunting-start.png)

2. 當您開啟**追捕**頁面上，所有追捕查詢會顯示在單一資料表。 下表列出所有的安全性分析師，以及您建立或修改任何其他查詢的 Microsoft 小組所撰寫的查詢。 每個查詢提供它，化搜尋和其執行所在的資料類型的描述。 這些範本會依其各種不同的策略-右邊的圖示將分類的威脅，例如初始的存取，持續性和滲透類型。 您可以篩選這些追捕查詢範本，使用的任何欄位。 您可以將任何查詢儲存至 [我的最愛]。 藉由將查詢儲存到 [我的最愛] 中，查詢會自動執行每次**追捕**存取頁面。 您可以建立您自己的追捕查詢或複製，並自訂現有追捕查詢範本。 
 
2. 按一下 **執行查詢**追捕中查詢詳細資料頁面，即可執行任何查詢，而不需要離開追捕頁面。  在資料表中顯示的相符項目數。 檢閱追捕查詢和在比對的清單。 查看相符項目相關聯的狙殺鏈中的哪一個階段。

3. 執行基礎查詢的快速檢閱查詢詳細資料 窗格中，或按一下**檢視查詢結果**開啟 Log Analytics 中的查詢。 在底部，請檢閱查詢的相符項目。

4.  按一下資料列，然後選取**新增書籤**來加入資料列，來調查-您可以針對任何項目看起來有問題。 

5. 然後，回到主**追捕**頁面，然後按一下**書籤**索引標籤來查看所有可疑的活動。 

6. 選取書籤，然後按一下**調查**開啟調查體驗。 您可以篩選書籤。 比方說，如果您要調查活動，您可以建立行銷活動的標記，並接著篩選活動為基礎的所有書籤。

1. 您會發現追捕查詢提供最高值可能攻擊的深入資料之後，您也可以建立自訂偵測規則根據您的查詢，並呈現為警示的深入資訊，以您的安全性事件回應。

 

## <a name="query-language"></a>查詢語言 

在 Azure Sentinel 追捕根據 Azure Log Analytics 查詢語言。 如需有關的查詢語言和支援的運算子的詳細資訊，請參閱[查詢語言參考](https://docs.loganalytics.io/docs/Language-Reference/)。

## <a name="public-hunting-query-github-repository"></a>公用追捕查詢 GitHub 存放庫

請參閱[追捕查詢儲存機制](https://github.com/Azure/Orion)。 參與和使用由我們的客戶共用的範例查詢。

 

## <a name="sample-query"></a>範例查詢

一般查詢開頭的資料表名稱，後面接著一系列的運算子分隔\|。

在範例中，開始使用資料表名稱 SecurityEvent 並視需要新增管道的元素。

1. 定義時間篩選器來檢閱只會從過去七天的記錄。

2. 新增篩選成只顯示事件識別碼 4688 查詢中。

3. 在查詢中包含的 cscript.exe 的執行個體的命令列上加入篩選。

4. 只有資料行投影您感興趣探索並將結果限制為 1000年，然後按一下**執行查詢**。
5. 按一下綠色的三角形，然後執行查詢。 您可以測試查詢，並加以執行，以尋找異常的行為。

## <a name="useful-operators"></a>實用運算子

是功能強大的查詢語言，並有許多可用的運算子，以下列出一些有用的運算子：

**其中**-篩選資料表，以滿足述詞的資料列子集。

**摘述**-產生的資料表，彙總輸入資料表的內容。

**聯結**-合併的比對每個資料表中指定的資料行的值形成新的資料表的兩個資料表的資料列。

**計數**-輸入記錄集內傳回的記錄數目。

**頂端**-依指定的資料行排序傳回前 n 個記錄。

**限制**-傳回最多指定數目的資料列。

**專案**： 選取要包含、 重新命名或卸除的資料行，然後插入新的計算資料行。

**擴充**-建立導出資料行，並將它們附加至結果集。

**makeset** -傳回群組中的相異值的運算式會採用一組動態的 (JSON) 陣列

**尋找**-尋找跨一組資料表中符合述詞的資料列。

## <a name="save-a-query"></a>儲存查詢

您可以建立或修改查詢並將它儲存成您自己的查詢，或與位於相同的租用戶的使用者共用。

   ![儲存查詢](./media/tutorial-hunting/save-query.png)

建立新的追捕查詢：

1. 按一下 **新的查詢**，然後選取**儲存**。
2. 填入所有空白欄位，然後選取**儲存**。

   ![新增查詢](./media/tutorial-hunting/new-query.png)

複製並修改現有的追捕查詢：

1. 選取您想要修改的資料表中的追捕查詢。
2. 在您想要修改，並選取的查詢列中選取省略符號 （...）**複製查詢**。

   ![複製查詢](./media/tutorial-hunting/clone-query.png)
 

3. 修改查詢並選取**建立**。

   ![自訂查詢](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 Azure Sentinel 執行追捕調查。 若要深入了解 Azure Sentinel，請參閱下列文章：


- [使用 notebook 來執行自動化的追捕行銷活動](notebooks.md)
- [使用書籤來儲存時追捕的有趣資訊](bookmarks.md)