---
title: .NET 應用程式的 azure Application Insights 快照集偵錯工具升級 |Microsoft Docs
description: 如何升級 Azure App Service，或透過 Nuget 套件的最新版本的快照集偵錯工具
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632161"
---
# <a name="upgrading-the-snapshot-debugger"></a>升級快照集偵錯工具

若要為您的資料提供最佳的可能安全性，Microsoft 是移動離開 TLS 1.0 和 TLS 1.1 能夠很容易受到攻擊者。 如果您使用較舊版本的網站延伸模組，它將需要升級才能繼續作業。 本文件概述升級為最新版本的快照集偵錯工具所需的步驟。 如果您已啟用快照集偵錯工具使用的網站延伸模組，或如果您使用 SDK/Nuget 新增至您的應用程式，則需要有取決於兩個主要的升級路徑。 下面會討論這兩個升級的路徑。 

## <a name="upgrading-the-site-extension"></a>升級網站延伸模組

如果您啟用快照集偵錯工具使用網站擴充功能，您可以輕鬆地升級使用下列程序：

1. 登入 Azure 入口網站。
2. 瀏覽至您的 Application Insights 快照偵錯工具啟用的資源。 例如，Web 應用程式中，瀏覽至 App Service 資源：

   ![個別的 App Service 資源的螢幕擷取畫面，名為 「 DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 一旦您巡覽至您的資源，在 [概觀] 刀鋒視窗中按一下 Application Insights:

   ![三個按鈕的螢幕擷取畫面。 使用名稱 Application Insights 的 [中央] 按鈕已選取](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. 新的刀鋒視窗會開啟與目前的設定。 除非您想要利用這個機會來變更您的設定，您可以保留現狀。 **套用**預設不啟用 [] 刀鋒視窗底部的按鈕，您必須切換設定，以啟動按鈕的其中一個。 您不需要變更任何實際的設定，而不是您可以在 變更設定，並回立即變更它。 我們建議您切換設定，然後選取 Profiler**套用**。

   ![「 以紅色反白顯示的 [套用] 按鈕的螢幕擷取畫面的 Application Insights 應用程式服務設定頁面](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. 一旦您按一下**套用**，系統會要求您確認變更。

    > [!NOTE]
    > 站台將會重新啟動升級程序的一部分。

   ![螢幕擷取畫面的 App Service 的適用於監視的提示字元。 文字方塊會顯示訊息：」 我們會立即將變更套用至您的應用程式設定並安裝我們的工具連結至 web 應用程式的 Application Insights 資源。 這會重新啟動站台。 您要繼續嗎？](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. 按一下 **是**以套用變更。 在程序期間會顯示正在套用變更出現通知：

   ![套用變更-正在更新延伸模組的訊息，會出現在右上角的螢幕擷取畫面](./media/snapshot-debugger-upgrade/updating-extensions.png)

一旦完成之後， **"的變更會套用 「** 會出現通知。

   ![套用的訊息，指出變更螢幕擷取畫面](./media/snapshot-debugger-upgrade/changes-are-applied.png)

網站現在已升級，並且可供使用。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>升級使用 SDK/Nuget 的快照集偵錯工具

如果應用程式使用新版`Microsoft.ApplicationInsights.SnapshotCollector`下方 1.3.1 版，它必須升級至[新版](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)繼續運作。
