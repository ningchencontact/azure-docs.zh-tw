---
title: Preview use cases - Azure Time Series Insights | Microsoft Docs
description: Learn about Azure Time Series Insights Preview use cases.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/19/2019
ms.custom: seodec18
ms.openlocfilehash: 92d738542076b755a26e8cff2e7fb1aa0384cb22
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227738"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure 時間序列深入解析預覽版使用案例

This article summarizes several common use cases for Azure Time Series Insights Preview. The recommendations in this article serve as a starting point to develop your applications and solutions with Time Series Insights.

Specifically, this article answers the following questions:

* 時間序列深入解析有哪些常見的使用案例？
* What are the benefits of using Time Series Insights for [data exploration and visual anomaly detection](#data-exploration-and-visual-anomaly-detection)?
* What are the benefits of using Time Series Insights for [operational analysis and process efficiency](#operational-analysis-and-driving-process-efficiency)?
* What are the benefits of using Time Series Insights for [advanced analytics](#advanced-analytics)?

An overview of these use scenarios is described in the following sections.

## <a name="introduction"></a>簡介

Azure Time Series Insights is an end-to-end, platform-as-a-service offering. 它可用來收集、處理、儲存、分析及查詢高度內容相關且已進行時間序列最佳化的 IoT 級別資料。 時間序列深入解析十分適用於特定資料探索及營運分析。 Time Series Insights is a uniquely extensible, customized service offering that meets the broad needs of industrial IoT deployments.

## <a name="data-exploration-and-visual-anomaly-detection"></a>資料探索與視覺化的異常偵測

立即探索與分析數十億個事件，並找出異常行為及發掘資料背後隱藏的趨勢。 時間序列深入解析能為您的 IoT 和 DevOps 分析工作負載提供近乎即時的效能。

[![Data explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Most customers agree that the minimal amount of time required to gain insight is one of the standout features of Time Series Insights:

* 時間序列深入解析不必事先準備資料。 
* It works fast to connect you to billions of events in your Azure IoT Hub or Azure Event Hubs instances in minutes. 
* 一旦連線，您就可以視覺化與分析數十億個事件，並找出異常行為及發掘資料背後隱藏的趨勢。

時間序列深入解析直觀且容易使用。 您不需撰寫任何一行程式碼，即可與您的資料互動。 There’s also no new language you're required to learn, although Time Series Insights provides a granular text-based querying language for advanced users who are familiar with SQL. 它還為新手提供了選取及點選探索。

Customers can take advantage of the speed to diagnose asset-related issues quickly. They can perform DevOps analysis to get to the root cause of a bug in an IoT solution. They also can identify areas to flag for further investigation as part of their data science initiatives. 

與時間序列深入解析中儲存的資料進行互動有三種主要方式：

* 第一個也是最簡單的方式是使用時間序列深入解析預覽版總管。 您可以使用它在同一個地方，快速地以視覺化方式檢視您所有的 IoT 資料。 It provides tools like the heat map to help you spot anomalies in your data. 它還提供了透視圖。 您可以使用它來比較一個儀表板中一或多個時間序列深入解析環境內最多四個檢視。 儀表板可讓您檢視所有位置的時間序列資料。 深入了解[時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。 若要規劃您的時間序列深入解析環境，請閱讀[時間序列深入解析規劃](./time-series-insights-update-plan.md)。

* The second way to start is to use the JavaScript SDK to quickly embed powerful charts and graphs in your web application. 只需幾行程式碼，您就可以撰寫功能強大的查詢。 Use them to populate line charts, pie charts, bar charts, heat maps, data grids, and more. 透過使用 SDK，所有這些元素都是立即可用。 SDK 還會擷取時間序列深入解析查詢 API。 您可以使用它們來撰寫類似 SQL 的述詞，以查詢要在儀表板上顯示的資料。 如需混合式展示層解決方案，時間序列深入解析會提供參數化的 URL。 它們提供與時間序列深入解析預覽版總管的無縫連接點，以深入探討資料。

  * Read about the [Time Series Insights JS client library](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) and the [Time Series Insights client](https://github.com/Microsoft/tsiclient) documentation to learn more about the JavaScript SDK.

  * Learn more about sharing URLs and the new UI by reviewing [Visualize data in the Azure Time Series Insights Preview explorer](time-series-insights-update-explorer.md).

* 第三個方式是使用強大的 API 來查詢儲存在時間序列深入解析中的資料。 Time Series Insights has temporal operators such as `from`, `to`, `first`, and `last`. It has aggregations and transformations such as `average`, `min`, `max`, `split by`, `order by`, and `DateHistogram`. It also has filtering operators such as `has`, `in`, `and`, `or`, `greater than`, and `REGEX`. 所有這些運算子使下游應用程式能夠快速尋找資料中的有趣趨勢和模式。 Use them to populate homegrown visualizations to spot anomalies.

## <a name="operational-analysis-and-driving-process-efficiency"></a>維運分析與推動流程效率

使用時間序列深入解析可大規模監視設備的健康情況、使用方式和效能。 時間序列深入解析提供了一種衡量營運效率的簡單方法。 時間序列深入解析可協助您管理各式各樣無法預測的 IoT 工作負載，而且您不必犧牲內嵌或查詢的效能。

[![概觀](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

如果與正確的技術或解決方案相結合，來自營運流程之資料的串流和連續處理，可以成功地轉換任何業務。 通常這些解決方案是多個系統的組合。 They enable exploration and analysis of data that changes constantly, especially in the IoT realm, and share a common pattern.

這些模式通常會從啟用 IoT 的平台開始，這些平台從跨越各種地區設定的裝置和感應器中內嵌數十億個事件。 這些系統會處理並分析資料流資料，以衍生即時的資訊分析與動作。 Data is typically archived to warm and cold store for near real-time and batch analytics.

收集的資料會經過一連串的處理，以便針對下游查詢和分析案例來清理資料並加以語境化。 Azure 提供豐富的服務，可套用至像是資產維護和製造等 IoT 案例。 這些服務包括時間序列深入解析、IoT 中樞、事件中樞、 Azure 串流分析、Azure Functions、Azure Logic Apps、Azure Databricks、Azure Machine Learning 和 Power BI。

解決方案架構可以透過下列方式來達成：

* 透過 IoT 中樞或事件中樞內嵌資料，以獲得最佳的安全性、輸送量和延遲。
* 執行資料處理和計算。 漏斗圖透過 Stream Analytics、Logic Apps 和 Azure Functions 等服務內嵌數據。 您所使用的服務取決於特定的資料處理需求。
* 來自處理管線的計算訊號會推送至時間序列深入解析進行儲存及分析。

時間序列深入解析提供近乎即時的資料探索，以及針對歷程記錄資料的資產型見解。 根據您的業務需求，MapReduce 和 Hive 作業可以藉由將時間序列深入解析連接到 Azure HDInsight，於儲存在時間序列深入解析中的資料上執行。 儲存在時間序列深入解析中的資料，可透過時間序列深入解析公用介面查詢 API 提供給 Power BI 和其他客戶應用程式使用。 這項資料可用於深入的業務和營運智慧案例。

## <a name="advanced-analytics"></a>進階分析

與 Machine Learning 和 Azure Databricks 這類進階分析服務整合。 時間序列深入解析會輸入來自數百萬個裝置的未經處理資料。 它會加入可由 Azure 分析服務套件順暢使用的內容相關資料。

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

進階分析和機器學習服務會取用並處理大量的資料。 這項資料用來制定資料導向決策，並執行預測性分析。 在 IoT 使用案例中，進階分析演算法會從來自數百萬個裝置所收集的資料中學習。 這些裝置每秒會傳輸多次資料。 從 IoT 裝置所收集的資料是未經處理的。 它缺少了內容相關資訊，例如裝置的位置和感應器讀取的單位。 因此，未經處理的資料很難直接供進階分析使用。

時間序列深入解析以兩種簡單且符合成本效益的方式，彌補了 IoT 資料與進階分析之間的差距：

* 首先，時間序列深入解析藉由使用 IoT 中樞，從數百萬台裝置收集原始遙測資料。 它透過內容相關資訊來充實資料，並將資料轉換成 parquet 格式。 這種格式可以輕鬆地與其他進階分析服務整合，例如機器學習、Azure Databricks 和第三方應用程式。

    時間序列深入解析可以作為整個組織中所有資料的真實來源。 它為下游分析工作負載建立了一個中央儲存機制。 因為時間序列深入解析是近乎即時的儲存體服務，因此進階分析模型可以從內送的 IoT 遙測資料中不斷學習。 如此一來，模型可以進行更精確的預測。

* Second, the output of machine learning and prediction models can be fed into Time Series Insights to visualize and store their results. 此程序可協助組織最佳化並調整其模型。 時間序列深入解析可讓在與定型模型輸出相同的平面上視覺化串流遙測資料變得簡單。 如此一來，它可以協助資料科學小組找出異常狀況並識別模式。

## <a name="next-steps"></a>後續步驟

* 深入了解[時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。
* Read [Time Series Insights Preview planning](./time-series-insights-update-plan.md) to plan out your environment.
* 請閱讀[時間序列深入解析用戶端](https://github.com/Microsoft/tsiclient)文件。
