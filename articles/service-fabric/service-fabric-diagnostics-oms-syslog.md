---
title: 監視 Azure Service Fabric 中的 Linux 叢集事件 | Microsoft Docs
description: 了解如何從 Syslog 監視 Linux 叢集事件
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 402e3dfe018c94ef068caf918b38aaad00064a49
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670365"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Syslog 中的 Service Fabric Linux 叢集事件

Service Fabric 會公開一組平台事件，以通知您叢集中的重要活動。 [此處](service-fabric-diagnostics-event-generation-operational.md)有完整的公開事件清單。 使用這些事件的方式有很多種。 在本文中，我們將討論如何設定 Service Fabric 來將這些事件寫入 Syslog。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>簡介

在 6.4 版中，已引入 SyslogConsumer 將 Service Fabric 平台事件傳送至 Linux 叢集的 Syslog。 一旦開啟，事件就會自動流向由 Log Analytics 代理程式收集及傳送的 Syslog。

每個 Syslog 事件有 4 個元件
* Facility
* 身分識別
* 訊息
* 嚴重性

SyslogConsumer 會使用 `Local0` 設施 (Facility) 寫入所有平台事件。 您可以藉由變更組態設定來更新為任何有效的設施。使用的身分識別是 `ServiceFabric`。 [訊息] 欄位會包含整個以 JSON 序列化的事件，以便各種工具查詢及使用。 

## <a name="enable-syslogconsumer"></a>啟用 SyslogConsumer

若要啟用 SyslogConsumer，您需要執行叢集升級。 `fabricSettings` 區段必須以下列程式碼更新。 請注意，此程式碼只會包含與 SyslogConsumer 相關的區段

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

以下是需要留意的變更
1. 在 [一般] 區段中，有一個稱為 `LinuxStructuredTracesEnabled` 的新參數。 **將 Linux 事件傳送給 Syslog 時，需要此參數才能將事件結構化及序列化。**
2. 在 [診斷] 區段中，新的 ConsumerInstance:SyslogConsumer 已新增。 這會告訴平台有其他事件取用者。 
3. 新的 SyslogConsumer 區段必須將 `IsEnabled` 設為 `true`。 這會自動設為使用 Local0 設施。 您可以藉由新增另一個參數來覆寫此設定。

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Azure 監視器記錄整合
您可以讀取這些監視工具，例如 Azure 監視器記錄檔中的 Syslog 事件。 您可以使用這些 [指示].(../azure-monitor/learn/quick-create-workspace.md)，透過 Azure Marketplace 建立 Log Analytics 工作區。您也必須將 Log Analytics 代理程式新增至您的叢集，以收集此資料並將其傳送至工作區。 這是用來收集效能計數器的相同代理程式。 

1. 瀏覽至 `Advanced Settings` 刀鋒視窗

    ![工作區設定](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. 按一下 `Data`
3. 按一下 `Syslog`
4. 將 Local0 設定要追蹤的設施。您可以在 fabricSettings 中進行變更，以新增另一個設施

    ![設定 Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. 按一下工作區資源功能表中的 `Logs`，即可前往 [查詢總管] 來開始查詢

    ![工作區記錄](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. 您可以根據 `Syslog` 資料表進行查詢，尋找作為 ProcessName 的 `ServiceFabric`。 下列查詢是如何剖析事件中 JSON 並顯示其內容的範例

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Syslog 查詢](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

上述範例中是 NodeDown 事件。 您可以在[此處](service-fabric-diagnostics-event-generation-operational.md)檢視完整的事件清單。

## <a name="next-steps"></a>後續步驟
* [部署 Log Analytics 代理程式](service-fabric-diagnostics-oms-agent.md)至您的節點，以收集效能計數器，並收集您容器的 Docker 統計資料和記錄
* 熟悉[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)作為 Azure 監視器記錄的一部分提供的功能
* [使用 Azure 監視器記錄檔中建立自訂檢視的檢視表設計工具](../log-analytics/log-analytics-view-designer.md)
* 如需參考[Azure 監視器會記錄與 Syslog 整合](../log-analytics/log-analytics-data-sources-syslog.md)。
