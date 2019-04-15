---
title: 使用 Azure 入口網站設定警示和通知 | Microsoft Docs
description: 使用 Azure 入口網站建立 SQL Database 的警示，在符合指定條件時觸發通知或自動化。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 93337e39a117c1f8d38f24dc416ff8ae95513a34
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855583"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>使用 Azure 入口網站建立 Azure SQL Database 和資料倉儲警示

## <a name="overview"></a>概觀
本文說明如何使用 Azure 入口網站設定 Azure SQL Database 和資料倉儲警示。 當某些計量 (例如，資料庫大小或 CPU 使用量) 閾值時，警示可傳送電子郵件給您或呼叫 Webhook。 本文也提供設定警示期間的最佳做法。    

> [!IMPORTANT]
> 此功能還無法在受控執行個體中取得。 或者，您可以根據[動態管理檢視](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) \(機器翻譯\)，針對某些計量使用 SQL 代理程式傳送電子郵件警示。

您可以收到以您 Azure 服務的監視計量或事件為基礎的警示。

* **計量值** - 當指定的計量值超出您在任一方向指派的臨界值時會觸發警示。 也就是說，當先符合條件而之後該條件不再符合時，兩方面皆會觸發。    
* **活動記錄檔事件** - 警示可觸發*每一個*事件，或是僅在發生特定事件數目時才觸發。

可以配置警报以在其触发时执行以下操作：

* 向服务管理员和共同管理员发送电子邮件通知
* 傳送電子郵件至您指定的其他電子郵件
* 调用 Webhook

您可以透過下列方式設定和取得有關警示規則的資訊

* [Azure 门户](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [命令行接口 (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>使用 Azure 入口網站建立計量的警示規則
1. 在 [入口網站](https://portal.azure.com/)中，找到您要監視的資源並選取。
2. 選取 [監視] 區段底下的 [警示 (傳統)]。 不同資源的文字和圖示會有些許不同。  
   
     ![監視](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **僅限 SQL DW**：按一下 [DWU 使用量] 圖表。 選取 [檢視傳統警示]

3. 選取 [新增計量警示 (傳統)] 按鈕，並填寫各欄位。
   
    ![新增警示](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. 為您的警示規則命名 ([名稱])，選擇將會顯示在電子郵件通知中的 [描述]。
5. 選取您要監視的 [計量]，然後為計量選擇 [條件] 和 [臨界值]。 同時選擇警示觸發程序之前，計量規則必須滿足的 [期間]。 例如，如果您使用「PT5M」期間，且您的警示會尋找高於 80% 的 CPU，當**平均** CPU 已高於 80% 5 分鐘，就會觸發警示。 一旦發生第一次觸發，它會在平均 CPU 低於 80 % 5 分鐘時再次觸發。 CPU 度量每隔 1 分鐘發生一次。 支援的時間範圍，請參閱下方的資料表，並彙總輸入每個警示會使用 not 所有警示都使用的平均值。   
6. 如果您想要在警示引發時傳送電子郵件給系統管理員和共同管理員，請勾選 [電子郵件的擁有者...]  。
7. 如果您想要讓其他電子郵件信箱在警示引發時收到通知，在 [其他系統管理員電子郵件]  欄位新增它們。 以分號區隔-分隔多個電子郵件*電子郵件\@contoso.com;email2\@contoso.com*
8. 如果您想在警示引發時呼叫webhook，在[webhook]  欄位中放入有效的 URI。
9. 完成後選取 [確定]  建立警示。   

在幾分鐘之內，警示會開始作用，且先前所述觸發。

## <a name="managing-your-alerts"></a>管理警报
一旦建立警示，您可以選取警示，並且︰

* 檢視圖表，其中顯示計量臨界值與前一天的實際值。
* 编辑或删除其。
* 如果您想要暫時停止或恢復接收警示的通知，可以**停用**或**啟用**警示。


## <a name="sql-database-alert-values"></a>SQL Database 警示值

| 資源類型 | 度量名稱 | 易記名稱 | 彙總類型 | 警示時間間隔下限|
| --- | --- | --- | --- | --- |
| SQL Database | cpu_percent | CPU 百分比 | 平均值 | 5 分鐘 |
| SQL Database | physical_data_read_percent | 資料 IO 百分比 | 平均值 | 5 分鐘 |
| SQL Database | log_write_percent | 記錄 IO 百分比 | 平均值 | 5 分鐘 |
| SQL Database | dtu_consumption_percent | DTU 百分比 | 平均值 | 5 分鐘 |
| SQL Database | 儲存體 | 数据库总大小 | 最大值 | 30 分钟 |
| SQL Database | connection_successful | 成功的連線 | 總計 | 10 分鐘 |
| SQL 数据库 | connection_failed | 失败的连接数 | 總計 | 10 分鐘 |
| SQL Database | blocked_by_firewall | 遭到防火牆封鎖 | 總計 | 10 分鐘 |
| SQL Database | 死結 | 死結 | 總計 | 10 分鐘 |
| SQL Database | storage_percent | 資料庫大小百分比 | 最大值 | 30 分钟 |
| SQL Database | xtp_storage_percent | In-Memory OLTP 存储百分比（预览） | 平均值 | 5 分鐘 |
| SQL Database | workers_percent | 背景工作角色百分比 | 平均值 | 5 分鐘 |
| SQL Database | sessions_percent | 工作階段百分比 | 平均值 | 5 分鐘 |
| SQL Database | dtu_limit | DTU 限制 | 平均值 | 5 分鐘 |
| SQL Database | dtu_used | 已使用 DTU | 平均值 | 5 分鐘 |
||||||
| 彈性集區 | cpu_percent | CPU 百分比 | 平均值 | 10 分鐘 |
| 彈性集區 | physical_data_read_percent | 資料 IO 百分比 | 平均值 | 10 分鐘 |
| 彈性集區 | log_write_percent | 記錄 IO 百分比 | 平均值 | 10 分鐘 |
| 彈性集區 | dtu_consumption_percent | DTU 百分比 | 平均值 | 10 分鐘 |
| 彈性集區 | storage_percent | 儲存體百分比 | 平均值 | 10 分鐘 |
| 彈性集區 | workers_percent | 背景工作角色百分比 | 平均值 | 10 分鐘 |
| 彈性集區 | eDTU_limit | eDTU 限制 | 平均值 | 10 分鐘 |
| 彈性集區 | storage_limit | 儲存體限制 | 平均值 | 10 分鐘 |
| 彈性集區 | eDTU_used | 已使用 eDTU | 平均值 | 10 分鐘 |
| 彈性集區 | storage_used | 已用的存储量 | 平均值 | 10 分鐘 |
||||||               
| SQL 資料倉儲 | cpu_percent | CPU 百分比 | 平均值 | 10 分鐘 |
| SQL 資料倉儲 | physical_data_read_percent | 資料 IO 百分比 | 平均值 | 10 分鐘 |
| SQL 資料倉儲 | connection_successful | 成功的連線 | 總計 | 10 分鐘 |
| SQL 資料倉儲 | connection_failed | 失败的连接数 | 總計 | 10 分鐘 |
| SQL 資料倉儲 | blocked_by_firewall | 遭到防火牆封鎖 | 總計 | 10 分鐘 |
| SQL 資料倉儲 | service_level_objective | 資料庫的服務層 | 總計 | 10 分鐘 |
| SQL 資料倉儲 | dwu_limit | dwu 限制 | 最大值 | 10 分鐘 |
| SQL 資料倉儲 | dwu_consumption_percent | DWU 百分比 | 平均值 | 10 分鐘 |
| SQL 資料倉儲 | dwu_used | 已用的 DWU | 平均值 | 10 分鐘 |
||||||


## <a name="next-steps"></a>後續步驟
* [取得 Azure 監視的概觀](../monitoring-and-diagnostics/monitoring-overview.md) 中說明您可以收集和監視的資訊類型。
* 深入了解 [在警示中設定 webhook](../azure-monitor/platform/alerts-webhooks.md)。
* 依照 [診斷記錄概觀](../azure-monitor/platform/diagnostic-logs-overview.md) 中的做法，收集您服務中詳細的高頻率計量。
* 依照 [計量集合概觀](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 中的做法，確保您的服務可使用且有回應。
