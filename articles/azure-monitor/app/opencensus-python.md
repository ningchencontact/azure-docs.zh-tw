---
title: 使用 Azure 監視器（預覽）監視 Python 應用程式 |Microsoft Docs
description: 提供將 OpenCensus Python 與 Azure 監視器連線的指示
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 4cd3d048ead8b9e6ff59a17d1a8269ecdec5a11c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750415"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>設定 Python 應用程式的 Azure 監視器（預覽）

Azure 監視器透過與[OpenCensus](https://opencensus.io)的整合，支援 Python 應用程式的分散式追蹤、度量集合和記錄。 本文將逐步引導您完成設定 Python 的 OpenCensus，以及將您的監視資料傳送至 Azure 監視器的程式。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- Python 安裝。 本文使用[Python 3.7.0](https://www.python.org/downloads/)，但較舊的版本可能會處理次要變更。



## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>在 Azure 監視器中建立 Application Insights 資源

首先，您必須在 Azure 監視器中建立 Application Insights 資源，這會產生檢測金鑰（ikey）。 然後，ikey 會用來設定 OpenCensus SDK，以將遙測資料傳送至 Azure 監視器。

1. 選取 [建立資源] > [開發人員工具] > [Application Insights]。

   ![新增 Application Insights 資源](./media/opencensus-python/0001-create-resource.png)

1. [設定] 方塊隨即出現。 使用下表來填寫輸入欄位。

   | 設定        | Value           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 全域唯一值 | 識別您要監視之應用程式的名稱 |
   | **資源群組**     | myResourceGroup      | 要裝載 Application Insights 資料之新資源群組的名稱 |
   | **位置** | 美國東部 | 您附近或接近應用程式裝載位置附近的位置 |

1. 選取 [建立]。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>使用適用于 Azure 監視器的 OpenCensus Python SDK 進行檢測

安裝 OpenCensus Azure 監視器匯出工具：

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> @No__t_0 命令會假設您已設定 Python 安裝的 `PATH` 環境變數。 如果您尚未設定此變數，您必須提供 Python 可執行檔所在位置的完整目錄路徑。 結果會是如下所示的命令： `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

SDK 會使用三個 Azure 監視器匯出工具，將不同類型的遙測傳送至 Azure 監視器：追蹤、計量和記錄。 如需這些遙測類型的詳細資訊，請參閱[資料平臺總覽](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 使用下列指示，透過這三個匯出工具傳送這些遙測類型。

### <a name="trace"></a>追蹤

1. 首先，讓我們在本機產生一些追蹤資料。 在 [Python 閒置] 或您選擇的編輯器中，輸入下列程式碼。

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. 執行程式碼會重複提示您輸入值。 在每個專案中，值都會列印到 shell，而 OpenCensus Python 模組會產生對應的 `SpanData` 片段。 OpenCensus 專案會將[追蹤定義為範圍的樹狀結構](https://opencensus.io/core-concepts/tracing/)。
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. 雖然輸入值對於示範用途很有説明，但我們還是會想要發出 `SpanData` 以 Azure 監視器。 根據下列程式碼範例，修改上一個步驟中的程式碼：

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. 現在，當您執行 Python 腳本時，仍然應該提示您輸入值，但只會在 shell 中列印該值。 建立的 `SpanData` 將會傳送至 Azure 監視器。 您可以在 `dependencies` 下找到發出的 span 資料。

### <a name="metrics"></a>計量

1. 首先，讓我們來產生一些本機計量資料。 我們將建立一個簡單的計量，以追蹤使用者按下 Enter 的次數。

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. 執行程式碼會重複提示您按下 Enter 鍵。 建立度量以追蹤按下 Enter 鍵的次數。 在每個專案中，值會遞增，而計量資訊會顯示在主控台中。 此資訊包括目前的值，以及計量更新時的目前時間戳記。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 雖然輸入值有助於示範用途，但最終我們會想要發出計量資料以 Azure 監視器。 根據下列程式碼範例，修改上一個步驟中的程式碼：

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. 匯出程式會以固定間隔將計量資料傳送至 Azure 監視器。 預設值為每15秒。 我們會追蹤單一計量，因此此計量資料（包含其內含的任何值和時間戳記）會每隔一段時間傳送。 您可以在 `customMetrics` 下找到資料。

### <a name="logs"></a>記錄

1. 首先，讓我們來產生一些本機記錄資料。

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  程式碼會持續要求輸入的值。 會針對每個輸入的值發出記錄專案。

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. 雖然輸入值有助於示範用途，但最終我們會想要發出計量資料以 Azure 監視器。 根據下列程式碼範例，修改上一個步驟中的程式碼：

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. 匯出程式會將記錄資料傳送至 Azure 監視器。 您可以在 `traces` 下找到資料。

## <a name="start-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中開始監視

1. 您現在可以在 Azure 入口網站中重新開啟 [Application Insights**總覽**] 窗格，以查看目前執行中應用程式的詳細資料。 選取 [**即時計量資料流**]。

   ![[總覽] 窗格的螢幕擷取畫面，其中已選取紅色方塊中的 [即時計量資料流]](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. 返回 [**總覽**] 窗格。 選取 [**應用程式對應**] 以取得相依性關聯性的視覺化版面配置，並在應用程式元件之間呼叫計時。

   ![基本應用程式對應的螢幕擷取畫面](./media/opencensus-python/0007-application-map.png)

   因為我們只追蹤一個方法呼叫，所以我們的應用程式對應並不有趣。 但是，應用程式對應可以進行調整，以視覺化更多分散式應用程式：

   ![應用程式對應](media/opencensus-python/application-map.png)

3. 選取 [**調查效能**] 以詳細分析效能，並判斷效能緩慢的根本原因。

   ![效能詳細資料的螢幕擷取畫面](./media/opencensus-python/0008-performance.png)

4. 若要開啟交易詳細資料的端對端體驗，請選取 [**範例**]，然後選取右窗格中出現的任何範例。 

   雖然我們的範例應用程式只會顯示單一事件，但更複雜的應用程式可讓您流覽至個別事件的呼叫堆疊層級的端對端交易。

   ![端對端交易介面的螢幕擷取畫面](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>使用查詢來查看您的資料

您可以透過 [**記錄（分析）** ] 索引標籤來查看您的應用程式所傳送的遙測資料。

![[總覽] 窗格的螢幕擷取畫面，其中選取了紅色方塊中的 [記錄（分析）]](./media/opencensus-python/0010-logs-query.png)

在 [作用中] 底下的清單**中：**

- 針對使用 Azure 監視器追蹤匯出工具傳送的遙測，傳入要求會顯示在 [`requests`] 之下。 傳出或同進程要求會出現在 `dependencies` 之下。
- 針對與 Azure 監視器計量匯出工具一起傳送的遙測，傳送的度量會顯示在 `customMetrics` 之下。
- 針對使用 Azure 監視器記錄匯出工具傳送的遙測，記錄會出現在 `traces` 之下。 例外狀況會出現在 `exceptions` 之下。

如需如何使用查詢和記錄的詳細資訊，請參閱[Azure 監視器中的記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="learn-more-about-opencensus-for-python"></a>深入瞭解適用于 Python 的 OpenCensus

* [在 GitHub 上 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自訂](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>後續步驟

* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警示

* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)：建立測試，以確保網路上看得見您的網站。
* [智慧型診斷](../../azure-monitor/app/proactive-diagnostics.md)︰這些測試會自動執行，您不需要採取任何動作來設定它們。 它們會讓您知道應用程式是否有不尋常的失敗要求率。
* 計量[警示](../../azure-monitor/app/alerts.md)：設定警示，在計量超出臨界值時警告您。 您可以在撰寫於程式碼中的自訂度量上設定它們。
