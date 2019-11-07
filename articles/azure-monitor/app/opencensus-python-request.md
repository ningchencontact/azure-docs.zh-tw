---
title: Azure 應用程式 Insights 中使用 OpenCensus Python 的傳入要求追蹤 |Microsoft Docs
description: 透過 OpenCensus Python 監視 Python 應用程式的要求呼叫。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: f6b06e7bb2bb8637ca28b2fa4185754f8686798e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587923"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>使用 OpenCensus Python 追蹤連入要求

使用 OpenCensus Python 和其各種整合來收集傳入的要求資料。 追蹤傳送至 web 應用程式的傳入要求資料，建置於熱門的 web 架構 `django`、`flask` 和 `pyramid`上。 然後，資料會以 `requests` 遙測傳送至 Azure 監視器下的 Application Insights。

首先，使用最新的[OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)檢測您的 python 應用程式。

## <a name="tracking-django-applications"></a>追蹤 Django 應用程式

1. 從[PyPI](https://pypi.org/project/opencensus-ext-django/)下載並安裝 `opencensus-ext-django`，並使用 `django` 中介軟體檢測您的應用程式。 將會追蹤傳送到您 `django` 應用程式的傳入要求。

2. 在 `MIDDLEWARE`下的 `settings.py` 檔案中包含 `opencensus.ext.django.middleware.OpencensusMiddleware`。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 請確定您在 `settings.py` 的 `OPENCENSUS`下已正確設定 AzureExporter。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
        }
    }
    ```

4. 您也可以將 url 新增至您不想要追蹤的要求 `BLACKLIST_PATHS` 下的 `settings.py`。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>追蹤 Flask 應用程式

1. 從[PyPI](https://pypi.org/project/opencensus-ext-flask/)下載並安裝 `opencensus-ext-flask`，並使用 `flask` 中介軟體檢測您的應用程式。 將會追蹤傳送到您 `flask` 應用程式的傳入要求。

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. 您可以直接在程式碼中設定您的 `flask` 中介軟體。 對於來自您不想要追蹤之 url 的要求，請將它們新增至 `BLACKLIST_PATHS`。

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>追蹤金字塔應用程式

1. 從[PyPI](https://pypi.org/project/opencensus-ext-pyramid/)下載並安裝 `opencensus-ext-django`，並使用 `pyramid` 的補間檢測您的應用程式。 將會追蹤傳送到您 `pyramid` 應用程式的傳入要求。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 您可以直接在程式碼中設定您的 `pyramid` 補間。 對於來自您不想要追蹤之 url 的要求，請將它們新增至 `BLACKLIST_PATHS`。

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    service_name='foobar',
                )''',
                'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>後續步驟

* [應用程式對應](../../azure-monitor/app/app-map.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
* [Search](../../azure-monitor/app/diagnostic-search.md)
* [記錄（分析）查詢](../../azure-monitor/log-query/log-query-overview.md)
* [交易診斷](../../azure-monitor/app/transaction-diagnostics.md)
