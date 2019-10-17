---
title: 將推播通知新增至 Xamarin.Android 應用程式 | Microsoft Docs
description: 了解如何使用 Azure App Service 及 Azure 通知中樞將推播通知傳送到 Xamarin.Android 應用程式
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 25e0196fec94acb363757e74fa71f666a4c9d11c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388495"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>將推播通知新增至 Xamarin.Android 應用程式

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center 支援行動應用程式開發的端對端和整合式服務中心。 開發人員可以使用**組建**、**測試**和**散發**服務來設定持續整合和傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用，並與使用**推**播服務的使用者互動。 開發人員也可以利用**驗證**來驗證其使用者和**資料**服務，以保存及同步雲端中的應用程式資料。
>
> 如果您想要在您的行動應用程式中整合雲端服務，請立即註冊[App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

## <a name="overview"></a>概觀

在本教學課程中，您會將推播通知新增至 [Xamarin.Android 快速入門](app-service-mobile-windows-store-dotnet-get-started.md)專案，以便在每次插入一筆記錄時傳送推播通知至裝置。

如果您不要使用下載的快速入門伺服器專案，則需要推播通知擴充套件。 如需詳細資訊，請參閱[使用適用於 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) 指南。

## <a name="prerequisites"></a>必要條件

本教學課程需要下列設定：

* 有效的 Google 帳戶。 您可以在 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)註冊 Google 帳戶。
* [Google Cloud Messaging 用戶端元件](https://components.xamarin.com/view/GCMClient/)。

## <a name="configure-hub"></a>設定通知中樞

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>啟用 Firebase 雲端傳訊

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>設定 Azure 來傳送推播要求

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>更新伺服器專案以傳送推播通知

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>設定用於推播通知的用戶端專案

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>將推播通知程式碼新增至應用程式

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>在應用程式中測試推播通知

您可以在模擬器中使用虛擬裝置來測試應用程式。 在模擬器上執行時，您需要採取其他設定步驟。

1. 虛擬裝置必須將 Google API 設定為 Android 虛擬裝置 (AVD) 中的目標。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. 按一下 [應用程式] > [設定] > [新增帳戶] 將 Google 帳戶加入 Android 裝置，然後遵循提示。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. 按照先前的方法執行 todolist 應用程式，然後插入新的 todo 項目。 這次，通知圖示會顯示在通知區域中。 您可以開啟通知抽屜來檢視通知的完整內容。

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
