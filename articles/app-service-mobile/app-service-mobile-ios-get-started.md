---
title: 在 App Service Mobile Apps 上建立 iOS 應用程式 | Microsoft Docs
description: 請遵循此教學課程開始使用 Azure 行動應用程式後端，用於 Objective-C 或 Swift 的 iOS 開發。
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 60190e0f8441d52b3d753e1dc79c67f480434dbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240268"
---
# <a name="create-an-ios-app"></a>建立 iOS 應用程式

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

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
