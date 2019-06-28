---
title: 調整 Azure 串流分析中的 Machine Learning 函式
description: 本文說明如何藉由設定資料分割和串流單位來調整使用 Machine Learning 函式的串流分析作業。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 5da09d705246ffd5002a1a21daab2266525f579e
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357504"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>調整 Stream Analytics 作業使用 Azure Machine Learning Studio 函式

這篇文章討論如何有效率地調整 Azure Stream Analytics 作業使用 Azure Machine Learning 函式。 如需有關如何調整串流分析作業的一般資訊，請參閱 [調整作業](stream-analytics-scale-jobs.md)文章。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>什麼是串流分析中的 Azure Machine Learning 函式？

串流分析中的 Machine Learning 函式可如同一般函式呼叫使用於串流分析查詢語言中。 不過，在幕後，這些函式呼叫是實際的 Azure Machine Learning Web 服務要求。

您可以 「 批次處理 」 多個資料列一起在相同的 web 服務 API 呼叫，以改善 Machine Learning web 服務要求的輸送量。 此分組中稱為迷你批次。 如需詳細資訊，請參閱 < [Azure Machine Learning Studio Web 服務](../machine-learning/studio/consume-web-services.md)。 Stream Analytics 中的 Azure Machine Learning Studio 支援處於預覽狀態。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>使用 Machine Learning 函式設定串流分析作業

有兩個參數來設定您的 Stream Analytics 工作使用 Machine Learning 函式：

* Machine Learning 函式呼叫的批次大小。
* 串流處理單位 (Su) 佈建的 Stream Analytics 作業數目。

若要判斷 Su 適當的值，決定您想要最佳化 Stream Analytics 作業的延遲或每個 SU 的輸送量。 但 su 一律會新增至作業，以提高妥善分割的 Stream Analytics 查詢的輸送量。 額外的 Su 增加執行作業的成本。

判斷延遲*tolerance*適用於您的 Stream Analytics 作業。 增加批次大小會增加您的 Azure Machine Learning 服務要求的延遲和 Stream Analytics 作業的延遲。

增加批次大小可讓處理 Stream Analytics 作業**更多的事件**具有**相同的數字**Machine learning web 服務要求。 Machine Learning web 服務延遲是通常是線性的批次大小增加。 

請務必考慮最符合成本效益的批次大小，在任何給定的情況下的 Machine Learning web 服務。 Web 服務要求的預設批次大小為 1000年。 您可以變更此預設使用的大小[Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API")或[Stream Analytics 的 PowerShell 用戶端](stream-analytics-monitor-and-manage-jobs-use-powershell.md)。

一旦您已決定批次大小，您可以設定數目的函式需要的事件數目為基礎的串流處理單位 (Su)，每秒處理序。 如需串流單位的詳細資訊，請參閱[串流分析調整作業](stream-analytics-scale-jobs.md)。

每 6 個 Su 取得 20 個對 Machine Learning web 服務的並行連線。 不過，1 SU 作業和 3 SU 作業取得 20 個同時連線。  

如果您的應用程式會產生每秒 200,000 個事件批次大小為 1000年，產生的 web 服務延遲是 200 毫秒。 此比率表示每個連接可以五個要求到 Machine Learning web 服務每秒。 透過 20 個連線，Stream Analytics 作業可以處理在 200 毫秒的 20,000 個事件和 100,000 個事件的每秒。

若要處理每秒 200,000 個事件，Stream Analytics 作業需要 40 的並行連線，結果出現 12 個 Su。 下圖說明串流分析作業向 Machine Learning Web 服務端點提出的要求︰每 6 個 SU 最多有 20 個對 Machine Learning Web 服務的同時連線。

![使用 Machine Learning 函數二作業範例調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "使用 Machine Learning 函數式二作業範例調整串流分析")

一般而言，***B*** 代表批次大小，***L*** 代表批次大小 B 的 Web 服務延遲 (以毫秒為單位)，而具有 ***N*** 個 SU 的串流分析作業的輸送量為：

![使用 Machine Learning 函式公式調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "使用 Machine Learning 函式公式調整串流分析")

您也可以在 Machine Learning web 服務設定 '最大同時呼叫 」。 建議將此參數設定的最大值 (目前為 200)。

如需此設定的詳細資訊，請參閱 [Machine Learning Web 服務的調整文章](../machine-learning/studio/scaling-webservice.md)。

## <a name="example--sentiment-analysis"></a>範例 – 情感分析
以下範例包含具有情感分析 Machine Learning 函式的串流分析作業，如 [串流分析 Machine Learning 整合教學課程](stream-analytics-machine-learning-integration-tutorial.md)所述。

此查詢是一個簡單的完全分割查詢，其後接著 **sentiment** 函式，如下列範例所示：

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

讓我們來檢查建立 Stream Analytics 作業，第二個的速率為每 10,000 則推文的推文的進行情感分析所需的設定。

使用 1 個 SU，可能此 Stream Analytics 作業處理的流量？ 作業可以跟輸入使用預設的批次大小 1000年。 預設的延遲時間情感分析 Machine Learning web 服務 （預設批次大小為 1000年） 會建立不超過一秒的延遲。

串流分析作業的 **整體** 或端對端延遲通常是幾秒鐘的時間。 更深入了解此串流分析作業，尤其是  Machine Learning 函式呼叫。 輸送量 10,000 個事件的批次大小 1000年，web 服務需要大約 10 個要求。 即使是一個 SU，也有足夠的同時連線可容納此輸入流量。

如果輸入事件速率增加 100 倍，則串流分析作業每秒需要處理 1,000,000 則推文。 有兩個選項可完成這種增加規模：

1. 增加批次大小。
2. 分割輸入資料流處理的事件，以平行方式。

使用第一個選項時，會增加作業**延遲**。

使用第二個選項時，您必須佈建更多 Su 有更多同時 Machine Learning web 服務要求。 這個更大的 Su 數目，會增加作業**成本**。

讓我們看看每個批次大小調整使用下列的延遲度量：

| Latency | 批大小 |
| --- | --- |
| 200 毫秒 | 1000 個事件批次或以下版本 |
| 250 毫秒 | 5,000 事件批次 |
| 300 毫秒 | 10000 個事件批次 |
| 500 毫秒 | 25000 事件批次 |

1. 使用第一個選項 (**不要**佈建更多 SU)。 批次大小可以增加到 **25,000**。 增加批次大小，以這種方式可利用來處理 1,000,000 個事件與 20 個對 Machine Learning web 服務的並行連線 （延遲為 500 毫秒，每次呼叫） 作業。 所以因為對 Machine Learning Web 服務要求的情感函式要求而產生的額外串流分析作業延遲會從 **200 毫秒**增加至 **500 毫秒**。 不過，批次大小**無法**無限增加，因為 Machine Learning web 服務所需的要求承載大小是 4 MB 否則較小的大小，以及作業 100 秒後，web 服務要求逾時。
1. 使用第二個選項，批次大小會保留為 1000、Web 服務延遲為 200 毫秒，則每 20 個對 Web 服務的同時連線就能夠處理 1000 * 20 * 5 個事件 = 每秒 100,000。 因此若要每秒處理 1,000,000 個事件，作業會需要 60 SU。 相較於第一個選項，串流分析作業會產生更多 Web 服務批次要求，進而使成本提高。

下表是不同 SU 和批次大小的串流分析作業輸送量 (以每秒的事件數目表示)。

| 批次大小 (ML 延遲) | 500 (200 毫秒) | 1,000 (200 毫秒) | 5,000 (250 毫秒) | 10,000 (300 毫秒) | 25,000 (500 毫秒) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SU** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SU** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

現在，您應該已充分了解串流分析中 Machine Learning 函式的運作方式。 您可能也了解串流分析作業會從資料來源「提取」資料，而且每次「提取」會傳回一批事件以供串流分析作業處理。 此提取模型如何影響 Machine Learning Web 服務要求？

一般來說，我們為 Machine Learning 函式設定的批次大小不會正好可被每次串流分析作業「提取」所傳回的事件數目除盡。 發生此狀況時，會以「部分」的批次呼叫 Machine Learning Web 服務。 使用部分的批次，可避免產生額外的作業延遲負擔聯合的事件中提取的。

## <a name="new-function-related-monitoring-metrics"></a>新的函式相關監視計量
在串流分析作業的 [監視] 區域中，已加入三個額外的函式相關計量。 它們**函式要求**，**函式事件**並**失敗的函式要求**，在下圖所示。

![使用 Machine Learning 函式計量調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "使用 Machine Learning 函式計量調整串流分析")

其定義如下：

**函式要求**：函式要求的數目。

**函式事件**：函式要求中的事件數目。

**失敗的函式要求**：失敗的函式要求數目。

## <a name="key-takeaways"></a>重要心得

若要調整 Stream Analytics 作業使用 Machine Learning 函式，請考慮下列因素：

1. 輸入的事件速率。
2. 適用於執行中的 Stream Analytics 作業 （和 Machine Learning web 服務要求的批次大小） 容許的延遲。
3. 佈建的 Stream Analytics Su 和 Machine Learning web 服務要求 （其他函式相關成本） 的數目。

以完全分割的串流分析查詢為例。 如果需要更複雜的查詢，[Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)是可向串流分析小組取得其他協助的絕佳資源。

## <a name="next-steps"></a>後續步驟
若要深入了解串流分析，請參閱：

* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
