---
title: 在 App Service Mobile Apps 上建立 iOS 應用程式 | Microsoft Docs
description: 請遵循此教學課程開始使用 Azure 行動應用程式後端，用於 Objective-C 或 Swift 的 iOS 開發。
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9140de50e23447f53fd1fa204a2a67c324672397
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449151"
---
# <a name="create-an-ios-app"></a>建立 iOS 應用程式

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 投入新的和整合式服務行動應用程式開發的核心。 開發人員可以使用**建置**，**測試**並**散發**services 設定持續整合和傳遞管線。 應用程式部署之後，開發人員可以監視的狀態和其應用程式使用的使用方式**Analytics**並**診斷**服務，並使用使用者參與**推播**服務。 開發人員也可以利用**Auth**來驗證使用者並**資料**保存和同步處理雲端中的應用程式資料的服務。 請參閱[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started)今天。
>

## <a name="overview"></a>概觀

本教學課程說明如何將 [Azure App Service Mobile Apps](app-service-mobile-value-prop.md) 這項雲端後端服務新增至 iOS 應用程式。 第一個步驟是在 Azure 上建立新的行動後端。 然後，下載會將資料儲存在 Azure 中的簡易*待辦事項清單* iOS 範例應用程式。

若要完成本教學課程，您需要一部 Mac 和 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>建立新的 Azure 行動應用程式後端

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>執行 iOS 應用程式

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
