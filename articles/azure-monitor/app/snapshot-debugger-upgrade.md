---
title: 適用于 .NET 應用程式的 Azure 應用程式 Insights 快照偵錯工具升級 |Microsoft Docs
description: 如何將快照偵錯工具升級至 Azure App 服務上的最新版本，或透過 Nuget 套件
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899842"
---
# <a name="upgrading-the-snapshot-debugger"></a>升級快照偵錯工具

為了為您的資料提供最佳的安全性，Microsoft 將從 TLS 1.0 和 TLS 1.1 中移出，這會被視為容易受到判斷攻擊者的攻擊。 如果您使用的是較舊版本的網站延伸模組，則需要升級才能繼續運作。 本檔概述將快照偵錯工具升級至最新版本所需的步驟。 根據您是使用網站擴充功能來啟用快照偵錯工具，還是使用已新增至應用程式的 SDK/Nuget，會有兩個主要的升級路徑。 以下將討論這兩種升級路徑。 

## <a name="upgrading-the-site-extension"></a>升級網站延伸模組

如果您使用網站延伸模組來啟用快照偵錯工具，您可以使用下列程式輕鬆地升級：

1. 登入 Azure 入口網站。
2. 流覽至已啟用 Application Insights 和快照偵錯工具的資源。 例如，針對 Web 應用程式，流覽至 App Service 資源：

   ![名為 DiagService01 之個別 App Service 資源的螢幕擷取畫面](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 導覽至您的資源之後，請按一下 [總覽] 分頁中的 [Application Insights]：

   ![三個按鈕的螢幕擷取畫面。 已選取 名稱為 Application Insights 的置中按鈕](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. 隨即開啟新的分頁，其中包含目前的設定。 除非您想要變更設定的機會，否則您可以將其保持原狀。 預設不會啟用分頁底部**的 [套用] 按鈕，而且**您必須切換其中一個設定來啟動按鈕。 您不需要變更任何實際的設定，而可以變更設定，然後立即將其變更回來。 建議您切換 Profiler 設定，**然後選取 [** 套用]。

   ![Application Insights App Service 設定頁面的螢幕擷取畫面，並以紅色反白顯示 [套用] 按鈕](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. **按一下 [** 套用] 之後，系統會要求您確認變更。

    > [!NOTE]
    > 此網站將會在升級過程中重新開機。

   ![App Service 的套用監視提示的螢幕擷取畫面。 文字方塊會顯示訊息：「我們現在會將變更套用至您的應用程式設定，並安裝我們的工具，以將您的 Application Insights 資源連結到 web 應用程式。 這會重新開機網站。 您要繼續嗎？](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. 按一下 **[是]** 以套用變更。 在過程中，將會出現通知，顯示正在套用的變更：

   ![顯示于右上角的 [套用變更-更新延伸模組] 訊息的螢幕擷取畫面](./media/snapshot-debugger-upgrade/updating-extensions.png)

完成後，將會出現「套用**變更**」通知。

   ![說明套用變更之訊息的螢幕擷取畫面](./media/snapshot-debugger-upgrade/changes-are-applied.png)

網站現已升級，並可供使用。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>使用 SDK/Nuget 升級快照偵錯工具

如果應用程式使用的版本 `Microsoft.ApplicationInsights.SnapshotCollector` 低於版本1.3.1，則必須將它升級為[較新的版本](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)，才能繼續運作。
