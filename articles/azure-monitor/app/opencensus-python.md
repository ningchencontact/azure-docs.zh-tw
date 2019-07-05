---
title: 監視使用 Azure Application Insights 的 Python 應用程式 |Microsoft Docs
description: 提供指示說明如何使用 Application Insights 的 OpenCensus Python 連接
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541442"
---
# <a name="collect-distributed-traces-from-python-preview"></a>從 Python 收集分散式追蹤 (預覽)

現在支援分散式追蹤與整合的 Python 應用程式的 application Insights [OpenCensus](https://opencensus.io)。 本文將逐步引導您逐步完成設定適用於 Python 的 OpenCensus 和取得 Application Insights 來監視資料的程序。

## <a name="prerequisites"></a>必要條件

- 您需要 Azure 訂用帳戶。
- 應該安裝 Python，本文使用 [Python 3.7.0](https://www.python.org/downloads/)，但舊版可能只需要稍微調整即可運作。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-application-insights-resource"></a>建立 Application Insights 資源

首先，您必須建立 Application Insights 資源，會產生檢測 key(ikey)。 Ikey 是再用來設定 OpenCensus SDK 將遙測資料傳送至 Application Insights。

1. 選取 [建立資源]   > [開發人員工具]   > [Application Insights]  。

   ![新增 Application Insights 資源](./media/opencensus-python/0001-create-resource.png)

   設定方塊隨即出現，請使用下表來填寫輸入欄位。

    | 設定        | 值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名稱**      | 通用唯一值 | 此名稱可識別您要監視的應用程式 |
   | **資源群組**     | myResourceGroup      | 用於裝載 App Insights 資料之新資源群組的名稱 |
   | **Location** | East US | 選擇您附近或接近應用程式裝載位置的地點 |

2. 按一下頁面底部的 [新增]  。

## <a name="install-opencensus-azure-monitor-exporters"></a>安裝 OpenCensus Azure 監視器匯出工具

1. 安裝 OpenCensus Azure 監視器匯出工具：

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` 假設您已設定 Python 安裝的 PATH 環境變數。 如果您尚未設定此變數，則可能需要提供 Python 可執行檔所在的完整目錄路徑，其會造成如下命令：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

2. 首先，讓我們在本機產生一些追蹤資料。 在 Python IDLE 或您選擇的編輯器中，輸入下列程式碼：

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

3. 執行程式碼會重複提示您輸入值。 隨著每次輸入，值會列印至殼層，並由 OpenCensus Python 模組產生對應的 **SpanData**。 OpenCensus 專案會[_將追蹤定義為範圍樹狀結構_](https://opencensus.io/core-concepts/tracing/)。
    
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

4. 雖然有幫助供示範之用，最後我們要發出至 Application Insights SpanData。 修改您的程式碼，根據下列的程式碼範例在上一個步驟：

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. 現在，當您執行 Python 指令碼中，您應該仍會提示您輸入值，但現在列印可以使用的值在 shell 中。

## <a name="start-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中開始監視

1. 現在，您可以在 Azure 入口網站中重新開啟 Application Insights 的 [概觀]  頁面，以檢視目前執行中應用程式的詳細資料。 選取 [即時計量串流]  。

   ![紅色方塊中選取了 [即時計量串流] 的 [概觀] 窗格螢幕擷取畫面](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. 瀏覽回到 [概觀]  頁面，然後選取 [應用程式對應]  ，以顯示應用程式元件之間相依性關係和呼叫計時的視覺化配置。

    ![基本應用程式對應的螢幕擷取畫面](./media/opencensus-python/0007-application-map.png)

    因為我們只追蹤一個方法呼叫，所以應用程式對應並不有趣。 但是，應用程式對應經過調整後，即可顯示更多的分散式應用程式：

   ![應用程式對應](media/opencensus-python/application-map.png)

3. 選取 [調查效能]  以執行詳細的效能分析，並判斷效能緩慢的根本原因。

    ![效能窗格的螢幕擷取畫面](./media/opencensus-python/0008-performance.png)

4. 選取 [範例]  ，然後按一下出現在右窗格中的任何範例，就會啟動端對端交易詳細資料體驗。 儘管應用程式範例只會向我們顯示單一事件，但更複雜的應用程式將可讓您瀏覽到個別事件呼叫堆疊層級的端對端交易。

     ![端對端交易介面的螢幕擷取畫面](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>適用於 Python 的 OpenCensus

* [自訂](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 整合](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>後續步驟

* [在 GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)