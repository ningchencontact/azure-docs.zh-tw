---
title: 在 Azure App Service Mobile Apps 上建立 Android 應用程式 | Microsoft Docs
description: 請遵循此教學課程，以開始使用 Azure 行動應用程式後端進行 Android 開發
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: emalani
ms.openlocfilehash: 238fde023ba1b5c8b21f181655c9633329c22699
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443601"
---
# <a name="create-an-android-app"></a>建立 Android 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 投入新的和整合式服務行動應用程式開發的核心。 開發人員可以使用**建置**，**測試**並**散發**services 設定持續整合和傳遞管線。 應用程式部署之後，開發人員可以監視的狀態和其應用程式使用的使用方式**Analytics**並**診斷**服務，並使用使用者參與**推播**服務。 開發人員也可以利用**Auth**來驗證使用者並**資料**保存和同步處理雲端中的應用程式資料的服務。 請參閱[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started)今天。
>

## <a name="overview"></a>概觀
本教學課程將示範如何使用 Azure 行動應用程式後端，將雲端式後端服務加入 Android 行動應用程式。  您將建立新的行動應用程式後端，以及可在 Azure 中儲存應用程式資料的簡易「待辦事項清單」  Android 應用程式。

對於在 Azure App Service 中使用 Mobile Apps 功能的所有其他 Android 教學課程，完成本教學課程是必要條件。

## <a name="prerequisites"></a>必要條件
若要完成此教學課程，您需要下列項目：

* [Android Developer Tools](https://developer.android.com/sdk/index.html)，其中包括 Android Studio 整合式開發環境，以及最新的 Android 平台。
* Azure 行動裝置的 Android SDK。
* 有效的 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-new-azure-mobile-app-backend"></a>建立新的 Azure 行動應用程式後端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>執行 Android 的應用程式
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
