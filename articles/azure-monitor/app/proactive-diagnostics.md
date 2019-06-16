---
title: Azure Application Insights 中的智慧型偵測 | Microsoft Docs
description: Application Insights 會自動深入分析您的 App 遙測，並且警告您有潛在的問題。
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: edbd7000001ae6927078e2f1bb9e348cc78f9efa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61299643"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights 中的智慧型偵測
 「智慧型偵測」會自動警告您 Web 應用程式中的可能效能問題。 它會針對您應用程式傳送給 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 的遙測執行主動式分析。 如果失敗率急遽上升或是用戶端或伺服器效能出現異常模式，您就會收到警示。 這項功能不需要進行任何設定。 只要您的應用程式傳送的遙測足夠，它就能發揮作用。

您可以從所收到的電子郵件和從 [智慧型偵測] 刀鋒視窗，存取「智慧型偵測」警示。

## <a name="review-your-smart-detections"></a>檢閱智慧型偵測
您可以透過兩種方式探索偵測︰

* **接收來自 Application Insights 的電子郵件** 。 以下是典型範例︰
  
    ![電子郵件警示](./media/proactive-diagnostics/03.png)
  
    按一下大型按鈕以在入口網站中開啟更多詳細資料。
* 您應用程式 [概觀] 刀鋒視窗上的 [主動式偵測]  磚會顯示最新進的警示計數。 按一下圖格即可查看最新警示的清單。

![檢視最近的偵測](./media/proactive-diagnostics/04.png)

選取警示來查看其詳細資料。

## <a name="what-problems-are-detected"></a>可偵測到哪些問題？
共有三種偵測︰

* [智慧型偵測 - 失敗異常](../../azure-monitor/app/proactive-failure-diagnostics.md)。 我們使用機器學習服務來為應用程式設定預期的失敗要求率，其會與負載和其他因素相互關聯。 如果失敗率超過預期限制，我們便會傳送警示。
* [智慧型偵測 - 效能異常](../../azure-monitor/app/proactive-performance-diagnostics.md)。 如果作業或相依性持續時間的回應時間變得比歷史基準慢，或如果我們在回應時間或頁面載入時間發現異常模式，您就會收到通知。   
* [智慧型偵測 - Azure 雲端服務問題](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/)。 如果您的應用程式裝載於 Azure 雲端服務，而且角色執行個體有啟動失敗、經常回收或執行階段損毀等現象，您便會收到警示。

(每個通知中的說明連結會帶您前往相關文章。)

## <a name="smart-detection-email-notifications"></a>智慧型偵測電子郵件通知

除了標示為預覽狀態之外，所有的智慧偵測規則均預設為發現到偵測時，傳送電子郵件通知。

開啟智慧型偵測 [設定]  刀鋒視窗，然後選取規則，即會開啟 [編輯規則]  刀鋒視窗，然後便可設定特定智慧偵測規則的電子郵件通知。

或者，您也可以使用 Azure Resource Manager 範本來變更設定。 如需詳細資訊，請參閱[使用 Azure Resource Manager 範本來管理 Application Insights 智慧偵測規則](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)。

## <a name="video"></a>影片

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>後續步驟
這些診斷工具可協助您檢查來自您的應用程式的遙測︰

* [計量瀏覽器](../../azure-monitor/app/metrics-explorer.md)
* [搜尋總管](../../azure-monitor/app/diagnostic-search.md)
* [分析 - 功能強大的查詢語言](../../azure-monitor/log-query/get-started-portal.md)

「智慧型偵測」是全自動的。 但是，或許您會想要再設定一些警示？

* [手動設定的度量警示](../../azure-monitor/app/alerts.md)
* [可用性 Web 測試](../../azure-monitor/app/monitor-web-app-availability.md) 

