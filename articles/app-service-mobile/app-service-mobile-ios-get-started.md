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
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: d0d6a3d9da2768c2d7b04bd9c4a7c24fba9eb65e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781675"
---
# <a name="create-an-ios-app"></a>创建 iOS 应用

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概觀

本教學課程說明如何將 [Azure App Service Mobile Apps](app-service-mobile-value-prop.md) 這項雲端後端服務新增至 iOS 應用程式。 第一個步驟是在 Azure 上建立新的行動後端。 然後，下載會將資料儲存在 Azure 中的簡易*待辦事項清單* iOS 範例應用程式。

若要完成本教學課程，您需要一部 Mac 和 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)

## <a name="step-i-create-a-new-azure-mobile-app-backend"></a>步驟 i:建立新的 Azure 行動應用程式後端

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="step-ii-configure-the-backend-project"></a>步驟 II︰設定後端專案

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="step-iii-download-and-run-the-ios-app"></a>步驟 III︰下載並執行 iOS 應用程式

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
