---
title: 瞭解 Visual Studio App Center 和 Azure 服務的行動應用程式使用方式和使用者行為
description: 瞭解 App Center 之類的服務，以瞭解使用者如何使用您的行動應用程式，以協助您做出明智的商業決策。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795792"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>分析並瞭解行動應用程式的使用方式
您要如何瞭解使用者如何使用您的應用程式？ 您的應用程式有多少使用中的使用者，以及使用量如何隨著時間變更？ 他們使用哪些功能，哪些是最常使用的？ 這些使用者以何為基礎？ 有多少使用者使用最新版的應用程式？ 所有這些問題都必須瞭解，才能將您的應用程式轉變成成功的業務。 若要回答這些類型的使用方式分析問題，您必須從您的應用程式收集使用量資料。

您的資料愈多，您可以找到改善應用程式的方法，讓您的使用者感到滿意。 請務必使用資料來尋找可操作的深入解析，並讓使用者感到滿意。

## <a name="importance-of-analytics"></a>分析的重要性
- **瞭解**您的使用者、他們如何與您的應用程式互動，以及如何讓他們回頭微調您的應用程式，並提供絕佳的體驗來拓展您的業務。
- **追蹤**您的使用計量，以做出明智的決策，決定如何行銷您的應用程式，並為客戶提供更好的服務。
- **測量**您的應用程式效能。
- **瞭解**應用程式的哪些部分會帶來價值和效能。
- **資料導向的深入**解析，涵蓋有關流失和保留的問題。

使用下列服務來啟用行動應用程式分析。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/)可讓您專注于重要事項，並深入瞭解使用者會話、熱門裝置、作業系統版本和行為分析，藉此拓展觀眾。 輕鬆地建立自訂事件，以追蹤任何具有大量應用程式分析的專案。

   **主要功能**
   - 免費**追蹤**使用模式、使用者採用和其他參與計量。
   - 透過自訂事件來**識別**趨勢、使用者行為和參與。
   - 在**單一儀表板**中的應用程式使用量（每日、每週、每月）、會話、裝置屬性和使用者人口統計的**現成計量**和**詳細見解**。
   - **持續將所有分析資料匯出至 Azure** ，以進行無限制的保留。 支援匯出至 Azure Blob 儲存體和 Azure 應用程式深入解析。
   - **與 Azure 應用程式 Insights 整合**，以獲得更深入的見解，例如保留、漏斗分析和世代
   - 單行**SDK 整合**，可在幾分鐘內開始使用。
   - **平臺支援**-IOS、Android、MacOS、TvOS、Xamarin、React Native、Unity、Cordova。

   **參考**
   - [使用 App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [開始使用 App Center 分析](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure 監視器包含[Application Insights](/azure/azure-monitor/app/app-insights-overview) ，其中提供收集和分析遙測的工具，以將效能最大化並監視您的行動應用程式。 您可以使用 App Center 分析來利用 Application Insights，並設定匯出至 Application Insights。 Application Insights 可以從您的應用程式查詢、分割、篩選及分析自訂事件遙測，但分析工具 App Center 提供的並不是。

**主要功能**
   - **查詢**您的自訂事件遙測。
   - 使用強大的分割功能來**篩選**事件遙測。
   - **分析**應用程式中的轉換、保留和瀏覽模式。
     - [漏斗圖] 可分析及監視轉換率。
     - **保留**以分析您的應用程式在一段時間內保留使用者的程度。
     - [活頁簿] 可將視覺效果和文字結合成可共用的報表。
     - [世代] 可命名或儲存特定使用者群組或事件，以方便從其他分析工具參考。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [使用 App Center 和 Application Insights 分析您的行動應用程式](/azure/azure-monitor/learn/mobile-center-quickstart)
- [使用 Application Insights 的使用方式分析](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/)提供完整的後端平臺，具備遊戲服務、即時分析和 LiveOps，您需要建立世界級的雲端連線遊戲。 這些服務可減少針對遊戲開發人員所啟動的障礙，同時提供大型和小型工作室符合成本效益的開發解決方案，以隨遊戲調整，並協助他們參與、留住和銷售玩家。 PlayFab 可讓開發人員使用智慧型雲端來建立和操作遊戲、分析遊戲資料，並改善整體遊戲體驗。

**主要功能**
   - **監視**即時儀表板。
   - 透過頂尖計量**評估**您的遊戲效能。
   - **報告**以透過自動產生的報告來審查遊戲的每日和每月效能摘要，並可在遊戲管理員中觀看，並每日下載或傳送到您的收件匣。
   - **A/B 測試，用**來執行實驗並判斷特定變數的最佳設定。
   - 適用于玩家的**分割**可讓您定義自動播放程式的分組。
    
**參考**
- [PlayFab 入口網站](https://developer.playfab.com/en-US/sign-up)
- [分析](/gaming/playfab/#pivot=documentation&panel=analytics)
- [快速入門](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
