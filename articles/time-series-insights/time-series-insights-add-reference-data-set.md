---
title: 如何將參考資料集新增至 Azure 時間序列深入解析環境 | Microsoft Docs
description: 本文說明如何新增參考資料集來擴展 Azure 時間序列深入解析環境中的資料。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: c33619ba52246128526bb132dfe94416c7c4de8c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299594"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>使用 Azure 入口網站建立時間序列深入解析環境的參考資料集

本文說明如何將參考資料集新增至 Azure 時間序列深入解析環境。 將參考資料聯結至您的來源資料很實用，可以增加資料值。

參考資料集是許多項目的集合，由這些項目來擴展您事件來源中的事件。 Time Series Insights 輸入引擎會將事件來源的每個事件和參考資料集中的對應資料聯結在一起。 然後此增強的事件可用於查詢。 這項聯結是以參考資料集中定義的主索引鍵資料行為基礎。

參考資料不會回溯加入。 因此，一旦設定並上傳，只有目前和未來的輸入資料會進行比對並加入參考日期集。

## <a name="video"></a>視訊

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>瞭解時間序列深入解析的參考資料模型。</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>新增參考資料集

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 找出您現有的 Azure 時間序列深入解析環境。 在 Azure 入口網站左側的功能表中選取 [所有資源]。 選取 Time Series Insights 環境。

1. 選取 [概觀] 頁面。 找出 [時間序列深入解析總管 URL] 並開啟連結。  

   查看時間序列深入解析環境的 [explorer]。

1. 展開 [時間序列深入解析 explorer] 中的環境選取器。 選擇使用中的環境。 在 [總管] 頁面右上方選取參考資料圖示。

   [@no__t 1Add 的參考資料](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. 選取 [+ 新增資料集] 按鈕，開始新增資料集。

   [@no__t 1Add 的資料集](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. 在 [新增參考資料集] 頁面上，選擇資料的格式：

   - 針對逗號分隔資料選擇 [CSV]。 第一個資料列會視為標題資料列。
   - 選擇適用于 JavaScript 物件標記法（JSON）格式資料的**JSON 陣列**。

   [@no__t 1Choose 的資料格式。](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. 使用兩種方法的其中一個來提供資料：

   - 將資料貼到文字編輯器中。 然後，選取 [剖析參考資料] 按鈕。
   - 選取 [選擇檔案] 按鈕，從本機文字檔新增資料。

   例如，將 CSV 資料貼上：[@no__t 1Pasted CSV 資料](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   例如，將 JSON 陣列資料貼上：[@no__t 1Paste JSON 資料](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   如果剖析資料值時發生錯誤，錯誤會在頁面的底部顯示為紅色，例如 `CSV parsing error, no rows extracted`。

1. 一旦成功剖析資料後，會出現資料格，顯示表示資料的資料行和資料列。  檢閱資料格，以確保正確性。

   [@no__t 1Add 的參考資料](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. 檢閱每個資料行以查看假設的資料類型，並視需要變更資料類型。  選取資料行標題中的資料類型符號： **#** 為 double (數值資料)、**T|F** 為布林值，或 **Abc** 為字串。

   [@no__t 欄標題上的1Choose 資料類型。](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. 視需要將資料行標題重新命名。 索引鍵資料行名稱必須加入至事件來源中的對應屬性。 請確定參考資料索引鍵資料行名稱與您傳入資料的名稱完全符合，包括區分大小寫。 非索引鍵資料行名稱會使用對應的參考資料值來擴充傳入資料。

1. 視需要選取 [加入資料列] 或 [**新增**資料**行**]，以加入更多參考資料值。

1. 在 [篩選資料列...] 欄位中輸入值，以視需要檢閱特定資料列。 篩選對於檢視資料非常有用，但在上傳資料時不會套用。

1. 若要為資料集命名，請在資料格上方的 [資料集名稱] 欄位中填入名稱。

    [![Name 資料集。](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. 選取資料格上方的下拉式清單，可提供資料集中的 [主索引鍵] 資料行。

    [![Select 索引鍵資料行。](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    選擇性選取 [+] 按鈕，以新增次要索引鍵資料行，作為複合主索引鍵。 如果您需要復原選取項目，請從下拉式清單中選擇空白值，以移除次要索引鍵。

1. 若要上傳資料，請選取 [上傳資料列] 按鈕。

    [![Upload](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    頁面會確認已完成上傳，並顯示 [已成功上傳資料集] 訊息。

## <a name="next-steps"></a>後續步驟

* 以程式設計的方式[管理參考資料](time-series-insights-manage-reference-data-csharp.md)。

* 如需完整的 API 參考，請參閱＜[參考資料 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)＞文件。
