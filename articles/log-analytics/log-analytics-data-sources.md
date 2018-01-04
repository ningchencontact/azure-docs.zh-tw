---
title: "設定資料來源中 Azure Log Analytics |Microsoft 文件"
description: "資料來源可定義 Log Analytics 要從代理程式和其他已連接的來源收集哪些資料。  本文說明 Log Analytics 如何使用資料來源的概念、詳細說明如何設定資料來源，並提供可用的不同資料來源的摘要。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2017
ms.author: bwren
ms.openlocfilehash: 4237df0934d6191b77ff82c86a66585e72191ac9
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="data-sources-in-log-analytics"></a>Log Analytics 中的資料來源
記錄分析會從您連接的來源收集資料，並將它儲存在您的記錄分析工作區中。  而您設定的資料來源可定義要從每個來源收集哪些資料。  記錄分析中的資料會儲存為一組記錄。  每個資料來源都會建立特定類型的記錄，每種類型各有自己的一組屬性。

![Log Analytics 資料收集](./media/log-analytics-data-sources/overview.png)

資料來源會不同於[管理解決方案](log-analytics-add-solutions.md)，這也會從 連線來源收集資料，並記錄分析中建立記錄。  除了收集資料，解決方案通常會包含的記錄搜尋和檢視，可協助您分析特定應用程式或服務的作業。


## <a name="summary-of-data-sources"></a>資料來源的摘要
下表列出 Log Analytics 中目前可用的資料來源。  每個資料來源都有個別的文章連結，提供該資料來源的詳細資料。

| 資料來源 | 事件類型 | 說明 |
|:--- |:--- |:--- |
| [自訂的記錄檔](log-analytics-data-sources-custom-logs.md) |\<LogName\>_CL |Windows 或 Linux 代理程式上包含記錄資訊的文字檔。 |
| [Windows 事件記錄檔](log-analytics-data-sources-windows-events.md) |Event |從事件登入 Windows 電腦收集事件。 |
| [Windows 效能計數器](log-analytics-data-sources-performance-counters.md) |Perf |從 Windows 電腦收集的效能計數器。 |
| [Linux 效能計數器](log-analytics-data-sources-performance-counters.md) |Perf |從 Linux 電腦收集的效能計數器。 |
| [IIS 記錄檔](log-analytics-data-sources-iis-logs.md) |W3CIISLog |W3C 格式的網際網路資訊服務記錄檔。 |
| [Syslog](log-analytics-data-sources-syslog.md) |syslog |Windows 或 Linux 電腦上的 Syslog 事件。 |

## <a name="configuring-data-sources"></a>設定資料來源
設定從資料來源**資料**記錄分析中的功能表**進階設定**。  任何組態會傳遞到您的工作區中的所有連接來源。  您目前無法透過這項組態來排除任何代理程式。

![設定 Windows 事件](./media/log-analytics-data-sources/configure-events.png)

1. 在 Azure 入口網站中，選取**記錄分析**> 您的工作區 >**進階設定**。
2. 選取 [資料] 。
3. 按一下您想要設定資料來源。
4. 參閱上表中每個資料來源的文件連結，即可取得其組態的詳細資訊。


## <a name="data-collection"></a>資料收集
系統會在幾分鐘內，將資料來源組態傳遞給直接連接到 Log Analytics 的代理程式。  系統會從代理程式收集指定的資料，並依據每個資料來源特定的時間間隔，直接傳遞給 Log Analytics。  請參閱每個資料來源的文件，以了解這些特性。

已連線的管理群組中的 System Center Operations Manager 代理程式，資料來源組態會轉譯為管理組件並傳遞至管理群組每隔 5 分鐘預設。  如同其他任何代理程式一樣，此代理程式也會下載管理組件，並收集指定的資料。 根據資料來源，資料將會是傳送到轉送資料至記錄分析中，管理伺服器或代理程式將資料傳送至記錄分析而不會經過管理伺服器。 請參閱[資料收集的詳細資料](log-analytics-add-solutions.md#data-collection-details)如需詳細資訊。  您可以閱讀詳細資料的連接 Operations Manager 與記錄分析和修改頻率該組態會在傳遞[Configure Integration with System Center Operations Manager](log-analytics-om-agents.md)。

如果代理程式無法連接到 Log Analytics 或 Operations Manager，它將會繼續收集資料，以便在建立連線時進行傳遞。  如果資料量達到用戶端的快取大小上限，或者代理程式無法在 24 小時內建立連線，可能就會遺失資料。

## <a name="log-analytics-records"></a>Log Analytics 記錄
記錄分析所收集的所有資料都儲存在工作區中，為記錄。  不同資料來源所收集的記錄會有自己的一組屬性，並由其 **類型** 屬性來識別。  如需每種記錄類型的詳細資訊，請參閱各資料來源和方案的文件。

## <a name="next-steps"></a>後續步驟
* 深入了解[解決方案](log-analytics-add-solutions.md)，將功能加入至記錄分析和也在工作區中收集資料。
* 了解 [記錄搜尋](log-analytics-log-searches.md) ，其可分析從資料來源和方案所收集的資料。  
* 設定[警示](log-analytics-alerts.md)，以便從資料來源和方案收集到重要資料時主動通知您。
