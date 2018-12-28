---
title: 收集與分析 Azure 監視器中的 Windows 事件記錄檔 | Microsoft Docs
description: 說明如何透過 Azure 監視器設定收集 Windows 事件記錄檔，以及它們建立記錄的詳細資料。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: a8c08eb222595b1531eef850667d3834d568b166
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435798"
---
# <a name="windows-event-log-data-sources-in-azure-monitor"></a>Azure 監視器中的 Windows 事件記錄檔資料來源
Windows 事件記錄檔是使用 Windows 代理程式收集資料的常見[資料來源](agent-data-sources.md)之一，因為許多應用程式會寫入 Windows 事件記錄檔。  除了指定您要監視之應用程式所建立的任何自訂記錄檔之外，您也可以透過標準記錄檔 (例如系統和應用程式) 來收集事件。

![Windows 事件](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>設定 Windows 事件記錄檔
從 [[進階設定] 中的 [資料] 功能表](agent-data-sources.md#configuring-data-sources)來設定 Windows 事件記錄檔。

Azure 監視器只會從設定中指定的 Windows 事件記錄檔收集事件。  您可以輸入記錄檔的名稱，然後按一下 **+**，來新增事件記錄檔。  針對每個記錄檔，僅會收集包含所選嚴重性的事件。  請檢查您想要收集之特定記錄檔的嚴重性。  您無法提供任何其他準則來篩選事件。

輸入事件記錄檔的名稱時，Azure 監視器提供常見的事件記錄檔名稱的建議。 如果您想要新增的記錄檔未出現在清單中，您仍然可以透過輸入記錄檔的完整名稱來新增它。 您可以使用事件檢視器來尋找記錄檔的完整名稱。 在事件檢視器中，開啟記錄檔的 [內容] 頁面，並從 [完整名稱] 欄位複製字串。

![設定 Windows 事件](media/data-sources-windows-events/configure.png)

## <a name="data-collection"></a>資料收集
在建立事件時，Azure 監視器會從受監視的事件記錄檔收集符合所選嚴重性的每個事件。  代理程式會在它收集的每個事件記錄檔中記錄它的位置。  如果代理程式離線一段時間，便會從上次停止的地方收集事件，即使這些事件是在代理程式離線時所建立亦同。  如果事件記錄檔是在代理程式離線時使用未收集且已遭覆寫的事件進行包裝，可能就無法收集這些事件。

>[!NOTE]
>Azure 監視器不會收集 SQL Server 從 *MSSQLSERVER* 來源用含有關鍵字 -  *Classic* 或 *Audit Success* 的事件 ID 18453 以及用關鍵字 *0xa0000000000000* 所建立的稽核事件。
>

## <a name="windows-event-records-properties"></a>Windows 事件記錄屬性
Windows 事件記錄都具有 **Event** 類型以及下表中的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 電腦 |收集事件的來源電腦名稱。 |
| EventCategory |事件的類別。 |
| EventData |原始格式的所有事件資料。 |
| EventID |事件的編號。 |
| EventLevel |數值格式的事件嚴重性。 |
| EventLevelName |文字格式的事件嚴重性。 |
| EventLog |收集事件的來源事件記錄檔名稱。 |
| ParameterXml |XML 格式的事件參數值。 |
| ManagementGroupName |System Center Operations Manager 代理程式的管理群組名稱。  若為其他代理程式，此值為 AOI-<workspace ID>。 |
| RenderedDescription |含參數值的事件描述 |
| 來源 |事件的來源。 |
| SourceSystem |收集事件的來源代理程式類型。 <br> OpsManager - Windows 代理程式，直接連接或由 Operations Manager 管理 <br> Linux – 所有的 Linux 代理程式  <br> AzureStorage – Azure 診斷 |
| TimeGenerated |在 Windows 中建立事件的日期和時間。 |
| UserName |記錄此事件之帳戶的使用者名稱。 |

## <a name="log-queries-with-windows-events"></a>使用 Windows 事件的記錄查詢
下表提供擷取 Windows 事件記錄的不同記錄查詢範例。

| 查詢 | 說明 |
|:---|:---|
| Event |所有的 Windows 事件。 |
| Event &#124; where EventLevelName == "error" |所有 Windows 事件與錯誤的嚴重性。 |
| Event &#124; summarize count() by Source |依據來源的 Windows 事件計數。 |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |依據來源的 Windows 錯誤事件計數。 |


## <a name="next-steps"></a>後續步驟
* 將 Azure 監視器設定成收集其他[資料來源](agent-data-sources.md)，以進行分析。
* 了解[記錄查詢](../../log-analytics/log-analytics-queries.md)，以分析從資料來源和解決方案收集到的資料。  
* 設定從您的 Windows 代理程式進行 [效能計數器收集](data-sources-performance-counters.md) 。
