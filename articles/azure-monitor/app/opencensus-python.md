---
title: 使用 Azure 監視器監視 Python 應用程式 |Microsoft Docs
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
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293995"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>設定 Python 應用程式的 Azure 監視器

Azure 監視器透過與[OpenCensus](https://opencensus.io)的整合，支援 Python 應用程式的分散式追蹤、度量收集和記錄。 本文將逐步引導您逐步完成設定 Python OpenCensus 的程式，並讓您的監視資料 Azure 監視器。

## <a name="prerequisites"></a>必要條件

- 您需要 Azure 訂用帳戶。
- 應該安裝 python，本文會使用[python 3.7.0](https://www.python.org/downloads/)，不過舊版可能會進行次要調整。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-application-insights-resource-in-azure-monitor"></a>在 Azure 監視器中建立 Application Insights 資源

首先，您必須在 Azure 監視器中建立 Application Insights 資源，這會產生檢測金鑰（ikey）。 然後，ikey 會用來設定 OpenCensus SDK，以將遙測資料傳送至 Azure 監視器。

1. 選取 [建立資源] > [開發人員工具] > [Application Insights]。

   ![新增 Application Insights 資源](./media/opencensus-python/0001-create-resource.png)

   設定方塊隨即出現，請使用下表來填寫輸入欄位。

    | 設定        | 值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 通用唯一值 | 此名稱可識別您要監視的應用程式 |
   | **資源群組**     | myResourceGroup      | 用於裝載 App Insights 資料之新資源群組的名稱 |
   | **位置** | East US | 選擇您附近或接近應用程式裝載位置的地點 |

2. 按一下 [建立]。

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>使用適用于 Azure 監視器的 OpenCensus Python SDK 進行檢測

1. 安裝 OpenCensus Azure 監視器匯出工具：

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` 假設您已設定 Python 安裝的 PATH 環境變數。 如果您尚未設定此變數，則可能需要提供 Python 可執行檔所在的完整目錄路徑，其會造成如下命令：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

2. SDK 會利用三個 Azure 監視器匯出工具，將不同類型的遙測傳送至 Azure 監視器：追蹤、計量和記錄。 如需這些不同類型的詳細資訊，請參閱[資料平臺總覽](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 請遵循下列指示，以瞭解如何透過這三個匯出工具來傳送這些不同的類型。

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

2. 執行程式碼會重複提示您輸入值。 隨著每次輸入，值會列印至殼層，並由 OpenCensus Python 模組產生對應的 **SpanData**。 OpenCensus 專案會[_將追蹤定義為範圍樹狀結構_](https://opencensus.io/core-concepts/tracing/)。
    
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

3. 雖然很適合用於示範，但我們還是會想要發出 `SpanData` 來 Azure 監視器。 根據下列程式碼範例，修改上一個步驟中的程式碼：

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

4. 現在，當您執行 Python 腳本時，仍然應該提示您輸入值，但現在只會在 shell 中列印該值。 建立的 `SpanData` 將會傳送至 Azure 監視器。 您可以在 `dependencies` 下找到發出的 span 資料。

### <a name="metrics"></a>計量

1. 首先，讓我們來產生一些本機計量資料。 我們將建立一個簡單的計量，以追蹤使用者按下 enter 的次數。

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
2. 執行程式碼會重複提示您按下 enter 鍵。 建立計量以追蹤按下的輸入數目。 在每個專案中，值會遞增，而計量資訊會顯示在主控台中，其中包含目前的值和計量更新時的目前時間戳記。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 雖然很適合用於示範，但我們還是會想要發出計量資料來 Azure 監視器。 根據下列程式碼範例，修改上一個步驟中的程式碼：

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

4. 匯出程式會將計量資料傳送至固定間隔的 Azure 監視器，預設值為每15秒一次。 我們會追蹤單一計量，讓此計量資料（其中包含的任何值和時間戳記）會每隔一個間隔傳送。 您可以在 `customMetrics` 下找到資料。

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

2.  程式碼會詢問持續提示輸入值。 針對每個輸入的值，都會發出一個記錄專案，其中包含所說的值。

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

3. 雖然很適合用於示範，但我們還是會想要發出計量資料來 Azure 監視器。 根據下列程式碼範例，修改上一個步驟中的程式碼：

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

1. 現在，您可以在 Azure 入口網站中重新開啟 Application Insights 的 [概觀] 頁面，以檢視目前執行中應用程式的詳細資料。 選取 [即時計量串流]。

   ![紅色方塊中選取了 [即時計量串流] 的 [概觀] 窗格螢幕擷取畫面](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. 瀏覽回到 [概觀] 頁面，然後選取 [應用程式對應]，以顯示應用程式元件之間相依性關係和呼叫計時的視覺化配置。

    ![基本應用程式對應的螢幕擷取畫面](./media/opencensus-python/0007-application-map.png)

    因為我們只追蹤一個方法呼叫，所以應用程式對應並不有趣。 但是，應用程式對應經過調整後，即可顯示更多的分散式應用程式：

   ![應用程式對應](media/opencensus-python/application-map.png)

3. 選取 [調查效能] 以執行詳細的效能分析，並判斷效能緩慢的根本原因。

    ![效能窗格的螢幕擷取畫面](./media/opencensus-python/0008-performance.png)

4. 選取 [範例]，然後按一下出現在右窗格中的任何範例，就會啟動端對端交易詳細資料體驗。 儘管應用程式範例只會向我們顯示單一事件，但更複雜的應用程式將可讓您瀏覽到個別事件呼叫堆疊層級的端對端交易。

     ![端對端交易介面的螢幕擷取畫面](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>使用查詢來查看您的資料

1. 您可以透過 [記錄（分析）] 索引標籤來查看您的應用程式所傳送的遙測資料。

    ![[總覽] 窗格的螢幕擷取畫面，其中包含在紅色方塊中選取的記錄（分析）](./media/opencensus-python/0010-logs-query.png)

2. 對於使用 Azure 監視器追蹤匯出工具傳送的遙測資料，傳入要求將會顯示在 `requests` 之下，且在處理要求中，將會顯示在 `dependencies` 之下。

3. 針對使用 Azure 監視器計量匯出工具傳送的遙測，傳送的計量會顯示在 `customMetrics` 之下。

4. 對於使用 Azure 監視器記錄匯出工具傳送的遙測，記錄會顯示在 `traces` 之下，且例外狀況會顯示在 `exceptions` 之下。

5. 如需如何使用查詢和記錄的詳細資訊，請參閱[Azure 監視器中的記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="opencensus-for-python"></a>適用于 Python 的 OpenCensus

* [在 GitHub 上 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自訂](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>後續步驟

* [API 摘要](./../../azure-monitor/app/api-custom-events-metrics.md)
* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警示

* [可用性測試](../../azure-monitor/app/monitor-web-app-availability.md)：建立測試，以確保網路上看得見您的網站。
* [智慧型診斷](../../azure-monitor/app/proactive-diagnostics.md)︰這些測試會自動執行，您不需要採取任何動作來設定它們。 它們會讓您知道應用程式是否有不尋常的失敗要求率。
* [計量警示](../../azure-monitor/app/alerts.md)：設定警示，在計量超出臨界值時警告您。 您可以在撰寫於程式碼中的自訂度量上設定它們。