---
title: "使用 Log Analytics 搭配 SQL Database 多租用戶應用程式 | Microsoft Docs"
description: "設定及使用 Log Analytics (OMS) 搭配多租用戶 Azure SQL Database SaaS 應用程式"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 7747092d5613a40fa0aff09cfbdfb9b786b37954
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>設定及使用 Log Analytics (OMS) 搭配多租用戶 Azure SQL Database SaaS 應用程式

在本教學課程中，您會設定及使用 *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))*，以便監視彈性集區和資料庫。 本教學課程是架構在[效能監視與管理教學課程](saas-dbpertenant-performance-monitoring.md)之上。 示範如何使用 *Log Analytics*，以增強 Azure 入口網站中提供的監視和警示功能。 Log Analytics 支援監視數千個彈性集區和數十萬個資料庫。 Log Analytics 也提供單一監視解決方案，可以跨多個 Azure 訂用帳戶，整合不同應用程式和 Azure 服務的監視。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 安裝及設定 Log Analytics (OMS)
> * 使用 Log Analytics 來監視集區和資料庫

若要完成本教學課程，請確定已完成下列必要條件：

* 已部署 Wingtip Tickets SaaS Database Per Tenant 應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip Tickets SaaS Database Per Tenant 應用程式](saas-dbpertenant-get-started-deploy.md)
* 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

請參閱[效能監視與管理教學課程](saas-dbpertenant-performance-monitoring.md)，以取得 SaaS 案例和模式的討論區，及其對於監視解決方案需求的影響。

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>使用 Log Analytics 或 Operations Management Suite (OMS) 來監視和管理資料庫和彈性集區的效能

針對 SQL Database，可在 Azure 入口網站使用資料庫和集區監視與警示功能。 此內建監視與警示雖方便，但限定於某些資源，比較不適合監視大型安裝，或提供橫跨資源和訂用帳戶的統一檢視。

對於高容量的情況，可以使用 Log Analytics 進行監視和警示。 Log Analytics 是個別的 Azure 服務，可透過收集在工作區中的診斷記錄和遙測資料 (可能來自許多服務) 進行分析。 Log Analytics 可提供內建的查詢語言和資料視覺化工具，以便進行操作資料分析。 SQL 分析解決方案提供了數個預先定義的彈性集區及資料庫監視與警示檢視和查詢。 OMS 也提供自訂檢視設計工具。

您可以在 Azure 入口網站和 OMS 中開啟 Log Analytics 工作區和分析解決方案。 Azure 入口網站是較新的存取點，但在某些區域可能位於 OMS 入口網站的後面。

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>藉由模擬您租用戶上的工作負載來建立效能診斷資料 

1. 在 **PowerShell ISE** 中開啟 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\**Demo-PerformanceMonitoringAndManagement.ps1***。 讓此指令碼保持開啟，因為您會在本教學課程期間執行數個負載產生案例。
1. 如果您尚未這麼做，請佈建一批租用戶以提供更有趣的監視內容。 這需要花幾分鐘的時間：
   1. 設定 **$DemoScenario = 1**，佈建一批租用戶
   1. 若要執行指令碼並部署額外的 17 個租用戶，請按 **F5**。  

1. 現在，啟動負載產生器，以在所有租用戶上執行模擬負載。  
    1. 設定 **$DemoScenario = 2**，產生一般強度負載 (約為 30 DTU)。
    1. 若要執行指令碼，按 **F5**。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>取得 Wingtip Tickets SaaS Database Per Tenant 應用程式指令碼

可在 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 存放庫中使用 Wingtip Tickets SaaS 多租用戶資料庫指令碼和應用程式來源程式碼。 關於下載和解除封鎖 Wingtip Tickets PowerShell 指令碼的步驟，請參閱[一般指引](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>安裝及設定 Log Analytics 和 Azure SQL 分析解決方案

Log Analytics 是需要設定的個別服務。 Log Analytics 會收集記錄分析工作區中的記錄資料、遙測以及計量。 就像 Azure 中的其他資源一樣，記錄分析工作區是必須建立的資源。 雖然工作區不一定要建立在與其監視之應用程式相同的資源群組中，但這麼做通常最有意義。 對於 Wingtip Tickets 應用程式而言，使用單一資源群組可確保工作區會與應用程式一起刪除。

1. 在 **PowerShell ISE** 中開啟 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***。
1. 若要執行指令碼，按 **F5**。

此時，您應可在 Azure 入口網站 (或 OMS 入口網站) 中開啟 Log Analytics。 需要花幾分鐘的時間才能在 Log Analytics 工作區中收集遙測並顯示出來。 您讓系統蒐集診斷資料的時間越長，體驗將會更有趣。 現在是放鬆一下的好時機 - 只要確定負載產生器仍在執行中！

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>使用 Log Analytics 和 SQL 分析解決方案來監視集區和資料庫


在此練習中，開啟 Log Analytics 和 OMS 入口網站以查看針對資料庫和集區蒐集的遙測資料。

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)，按一下 [所有服務] 以開啟 Log Analytics，然後搜尋 Log Analytics︰

   ![開啟 Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. 選取名為 wtploganalytics-&lt;user&gt; 的工作區。

1. 選取 [概觀] 以在 Azure 入口網站中開啟 Log Analytics 解決方案。

   ![overview-link](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > 該解決方案可能需要幾分鐘的時間才能變成作用中。 請耐心等候！

1. 按一下 [Azure SQL 分析] 圖格加以開啟。

    ![概觀](media/saas-dbpertenant-log-analytics/overview.png)

    ![分析](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. 解決方案中的檢視會橫向捲動，其在底部有自己的內部捲軸列 (視需要重新整理頁面)。

1. 若要瀏覽摘要頁面，您可以按一下圖格或個別的資料庫，以開啟向下鑽研的總管頁面。

1. 變更篩選設定可修改時間範圍，針對此教學課程，請挑選「過去 1 小時」

    ![時間篩選器](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. 選取單一資料庫，以探索該資料庫的查詢使用量和計量。

    ![資料庫分析](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. 若要查看使用計量，請向右捲動分析頁面。
 
     ![資料庫計量](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. 向左捲動分析頁面，然後按一下資源資訊清單中的伺服器圖格。 此動作會開啟頁面，顯示伺服器上的集區和資料庫。 

     ![資源資訊](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![具有集區和資料庫的伺服器](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. 開啟的伺服器頁面上顯示了伺服器上的集區和資料庫，請在上面按一下集區。  在開啟的集區頁面上，向右捲動到看到集區計量。  

     ![集區計量](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. 回到 Log Analytics 工作區，選取 **OMS 入口網站**，以在該處開啟工作區。

    ![oms](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

在 OMS 入口網站中，您可以進一步探索工作區中的記錄和計量資料。  

Log Analytics 和 OMS 中的監視和警示是以工作區中的資料查詢為基礎，不同於 Azure 入口網站中每個資源上定義的警示。 由於警示是以查詢為基礎，因此您可以定義可查看所有資料庫的單一警示，而不是為每個資料庫定義一個警示。 查詢只會受限於工作區中可用的資料。

如需有關使用 OMS 查詢並設定警示的詳細資訊，請參閱[在 Log Analytics 中使用警示規則](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)。

SQL Database 的 Log Analytics 需根據工作區中的資料量付費。 在本教學課程中，您建立了一個免費工作區，其每天的限制為 500 MB。 一旦達到該限制，工作區就不會再新增資料。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 安裝及設定 Log Analytics (OMS)
> * 使用 Log Analytics 來監視集區和資料庫

[租用戶分析教學課程](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>其他資源

* [在初始 Tickets SaaS Database Per Tenant 應用程式部署上建置的其他教學課程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
