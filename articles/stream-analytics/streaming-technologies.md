---
title: 選擇即時的分析和串流處理技術，在 Azure 上
description: 深入了解如何選擇向右即時分析和建置您的應用程式，在 Azure 上的串流處理技術。
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: f46a35d971c008b61d4899e30101ea562d3cefea
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483416"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>選擇即時的分析和串流處理技術，在 Azure 上

有數個服務的即時分析和 Azure 上的串流處理。 本文章提供您需要決定哪一種技術最適合您的應用程式的資訊。

## <a name="when-to-use-azure-stream-analytics"></a>使用 Azure Stream Analytics 的時機

Azure Stream Analytics 是 Azure 上的串流分析的建議的服務。 它適用於各種案例，包括但不限於：

* 資料視覺效果的儀表板
* 即時[警示](stream-analytics-set-up-alerts.md)從時態表和空間模式或異常行為
* 擷取、轉換、載入 (ETL)
* [事件溯源模式](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

新增 Azure Stream Analytics 作業，您的應用程式是最快的方法，取得設定串流分析，並使用 SQL 語言在 Azure 中執行您已經知道。 Azure Stream Analytics 是作業服務，您不必花時間管理的叢集，因此您不必擔心停機時間達 99.9 %sla，在作業層級。 計費也是在作業層級進行啟動成本低 （一個串流單位），但可擴充 （最多 192 串流處理單位）。 它會更符合成本效益比執行及維護叢集中執行幾項 Stream Analytics 工作。

Azure Stream Analytics 有豐富的立即可用的體驗。 您可以立即利用下列功能，而不需要任何額外的設定：

* 內建的時態性運算子，例如[視窗型彙總](stream-analytics-window-functions.md)，時態性聯結和時態性分析函數。
* 原生 Azure[輸入](stream-analytics-add-inputs.md)並[輸出](stream-analytics-define-outputs.md)配接器
* 支援變更緩慢[參考資料](stream-analytics-use-reference-data.md)（也稱為是查閱資料表），包括地理圍欄的地理空間的參考資料聯結。
* 整合式解決方案，例如[異常偵測](stream-analytics-machine-learning-anomaly-detection.md)
* 在相同的查詢中的多個時間範圍
* 撰寫任意序列中的多個時態性運算子的能力。
* 在 100 毫秒從輸入抵達事件中樞，將輸出事件中樞的登陸的端對端延遲會包括在持續的高輸送量的網路延遲從和到事件中樞，

## <a name="when-to-use-other-technologies"></a>使用其他技術的時機

### <a name="you-need-to-input-from-or-output-to-kafka"></a>您需要從輸入或輸出到 Kafka

Azure Stream Analytics 不會有 Apache Kafka 的輸入或輸出配接器。 如果您有事件置入，或需要將傳送至 Kafka，而且您沒有執行 Kafka 叢集的需求，您可以繼續使用 Stream Analytics 將事件傳送至事件中樞使用事件中樞 Kafka API，而不需要變更事件的寄件者。 如果您需要執行 Kafka 叢集，您可以使用 Spark 結構化串流，其上完全支援[Azure Databricks](../azure-databricks/index.yml)，或在 Storm [Azure HDInsight](../hdinsight/storm/apache-storm-overview.md)。

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>您想要在 JavaScript 以外的語言撰寫 Udf、 Uda 和自訂反序列化程式或C#

Azure Stream Analytics 支援使用者定義函數 (UDF) 或使用者定義彙總 (UDA) 在 JavaScript 中進行雲端作業與C#IoT Edge 作業。 C#也支援使用者定義的反序列化程式。 如果您想要還原序列化程式，UDF 或實作 UDA 以其他語言，例如 Java 或 Python，您可以使用 Spark 結構化串流。 您也可以執行的事件中樞**EventProcessorHost**進行任意的串流處理虛擬機器上。

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>您的解決方案是在多重雲端或內部部署環境

Azure Stream Analytics 是 Microsoft 專利技術，僅可在 Azure 上。 如果您需要能夠移植到不同雲端或內部部署解決方案，請考慮開放原始碼技術，例如 Spark 結構化串流或 Storm。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 入口網站建立 Stream Analytics 作業](stream-analytics-quick-create-portal.md)
* [使用 Azure PowerShell 建立 Stream Analytics 作業](stream-analytics-quick-create-powershell.md)
* [使用 Visual Studio 建立 Stream Analytics 作業](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio Code 建立 Stream Analytics 作業](quick-create-vs-code.md)