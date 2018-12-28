---
title: 在 Azure 監視器中收集 Nagios 和 Zabbix 警示 | Microsoft Docs
description: Nagios 和 Zabbix 是開放原始碼監視工具。 您可以將來自這些工具的警示收集到 Azure 監視器，以搭配其他來源的警示一起分析。  本文說明如何設定 Log Analytics Linux 代理程式以收集來自這些系統的警示。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 217f19b02ccd5561ee499388bb62d5a6639086ac
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436189"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>從 Log Analytics Linux 代理程式在 Azure 監視器中收集來自 Nagios 和 Zabbix 的警示 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
<<<<<<< HEAD [Nagios](https://www.nagios.org/) 和 [Zabbix](http://www.zabbix.com/) 是開放原始碼監視工具。 您可以將來自這些工具的警示收集到 Azure 監視器，以搭配其他來源的記錄資料一起分析。  本文說明如何設定 Log Analytics Linux 代理程式以收集來自這些系統的警示。

> [!NOTE]
> [Azure 監視器建立的警示](../../monitoring-and-diagnostics/monitoring-overview-alerts.md)會與記錄資料分開儲存，而且無法從記錄查詢中存取。
=======
[Nagios](https://www.nagios.org/) 和 [Zabbix](http://www.zabbix.com/) 是開放原始碼監視工具。 您可以將來自這些工具的警示收集到 Log Analytics，以搭配[其他來源的警示](../../azure-monitor/platform/alerts-overview.md)一起分析。  本文說明如何設定 Log Analytics Linux 代理程式以收集來自這些系統的警示。
>>>>>>> 98b6f1655e9ada6d4b6a408ada0f1e4c9ed727d2
 
## <a name="prerequisites"></a>必要條件
Log Analytics Linux 代理程式支援從 Nagios 收集的警示最高為 4.2.x 版，Zabbix 則最高為 2.x 版。

## <a name="configure-alert-collection"></a>設定警示收集

### <a name="configuring-nagios-alert-collection"></a>設定 Nagios 警示收集
若要收集警示，請在 Nagios 伺服器上執行下列步驟。

1. 將 Nagios 記錄檔 `/var/log/nagios/nagios.log` 的讀取權授與使用者 **omsagent**。 假設 nagios.log 檔案是由 `nagios` 群組所擁有，您可以將使用者 **omsagent** 新增至 **nagios** 群組。 

    sudo usermod -a -G nagios omsagent

2.  編輯位於 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 的組態檔。 確定下列項目存在且未標成註解︰  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. 重新啟動 omsagent 精靈

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>設定 Zabbix 警示收集
若要收集來自 Zabbix 伺服器的警示，您需要以「純文字」指定使用者名稱和密碼。  雖然不理想，但建議您建立具有唯讀權限的 Zabbix 使用者來擷取相關警示。

若要在 Nagios 伺服器上收集警示，請執行下列步驟。

1. 編輯位於 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 的組態檔。 確定下列項目存在且未標成註解。將使用者名稱和密碼變更為您的 Zabbix 環境值。

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. 重新啟動 omsagent 精靈

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>警示記錄
您可以在 Azure 監視器中使用[記錄查詢](../log-query/log-query-overview.md)，擷取來自 Nagios 和 Zabbix 的警示記錄。

### <a name="nagios-alert-records"></a>Nagios 警示記錄

Nagios 所收集之警示記錄的 **Type** 為 **Alert**，而 **SourceSystem** 為 **Nagios**。  其屬性如下表所示。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*警示* |
| SourceSystem |*Nagios* |
| AlertName |警示的名稱。 |
| AlertDescription | 警示的描述。 |
| AlertState | 服務或主機的狀態。<br><br>OK<br>WARNING<br>UP<br>DOWN |
| HostName | 建立警示的主機名稱。 |
| PriorityNumber | 警示的優先順序層級。 |
| StateType | 警示的狀態類型。<br><br>SOFT - 未重新檢查的問題。<br>HARD - 已依指定次數重新檢查的問題。  |
| TimeGenerated |建立警示的日期和時間。 |


### <a name="zabbix-alert-records"></a>Zabbix 警示記錄
Zabbix 所收集之警示記錄的 **Type** 為 **Alert**，而 **SourceSystem** 為 **Zabbix**。  其屬性如下表所示。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 |*警示* |
| SourceSystem |*Zabbix* |
| AlertName | 警示的名稱。 |
| AlertPriority | 警示的嚴重性。<br><br>未分類<br>資訊<br>警告<br>average<br>高<br>嚴重損壞  |
| AlertState | 警示的狀態。<br><br>0 - 最新狀態。<br>1 - 狀態未知。  |
| AlertTypeNumber | 指定警示是否可以產生多個問題事件。<br><br>0 - 最新狀態。<br>1 - 狀態未知。    |
| 註解 | 警示的其他註解。 |
| HostName | 建立警示的主機名稱。 |
| PriorityNumber | 指出警示嚴重性的值。<br><br>0 - 未分類<br>1- 資訊<br>2 - 警告<br>3 - 平均<br>4 - 高<br>5 - 嚴重損壞 |
| TimeGenerated |建立警示的日期和時間。 |
| TimeLastModified |上次變更警示狀態的日期和時間。 |


## <a name="next-steps"></a>後續步驟
* 深入了解 Azure 監視器中的[警示](../../monitoring-and-diagnostics/monitoring-overview-alerts.md)。
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。 
