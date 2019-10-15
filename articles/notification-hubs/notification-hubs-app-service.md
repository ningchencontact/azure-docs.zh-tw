---
title: 與 App Service Mobile Apps 整合
description: 了解如何搭配使用 Azure 通知中樞和 Azure App Service Mobile Apps。
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8c3bc90b282092ede0e924d32b50b67e5c4e22b8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244492"
---
# <a name="integration-with-app-service-mobile-apps"></a>與 App Service Mobile Apps 整合

為了在 Azure 服務間促進完美且統一的體驗， [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) 具備使用通知中樞提供推播通知的內建支援。 [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) 具有高擴充性且可供全球使用，是專為企業開發人員與系統整合人員設計的行動應用程式開發平台，能提供一組豐富的功能給行動應用程式開發人員。

Visual Studio App Center 為現今的開發人員提供了更現代的行動解決方案。 它支援使用端對端及整合服務中心來開發行動應用程式。 開發人員可以使用**建置**、**測試**和**散發**服務來設定持續整合及傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用情況，並使用**推送**服務與使用者互動。 開發人員也可以利用**驗證**來驗證其使用者，並使用**資料**來保存及同步雲端中的應用程式資料。

> [!NOTE]
> 如果您想要在行動應用程式中整合雲端服務，請立即註冊 [App Center](https://appcenter.ms/signup?utm_source=NotificationHubs&utm_medium=Azure&utm_campaign=docs) \(英文\)。

Mobile Apps 開發人員可以使用下列流程來利用通知中樞：

1. 擷取裝置 PNS 控制代碼
2. 透過便利的 Mobile Apps Client SDK 註冊 API，使用通知中樞註冊裝置

    > [!NOTE]
    > 請注意，Mobile Apps 會基於安全性考量，去除註冊上的所有標籤。 直接從後端使用通知中樞，將標籤關聯至裝置。

3. 從 App 後端使用通知中樞傳送通知

以下是透過此註冊為開發人員帶來的一些便利性：

- **Mobile Apps 用戶端 SDK**： 這些多平台 SDK 提供簡單的 API 來進行註冊，然後會自動與連結到行動 App 的通知中樞聯繫。 開發人員不需要透過通知中樞認證進行挖掘，以及使用其他服務。
  - *推送給使用者*︰SDK 會使用 Mobile Apps 驗證的使用者識別碼自動標記指定的裝置，以啟用推送至使用者案例。
  - *推送至裝置*︰SDK 會自動使用 Mobile Apps 安裝識別碼做為 GUID 來向通知中樞註冊，省去開發人員維護多個服務 GUID 的麻煩。
- **安裝模型**： Mobile Apps 會使用通知中樞的最新推送模型，來呈現 JSON 安裝中所有與裝置相關聯的推送屬性，其會與推播通知密切合作且易於使用。
- **彈性**： 即使已就地整合，開發人員一律還是可以選擇直接使用通知中樞。
- **[Azure 入口網站](https://portal.azure.com)中的整合體驗**： Mobile Apps 中會以視覺化方式呈現以功能形式出現的推送，而開發人員可以透過 Mobile Apps 輕鬆使用相關聯的通知中樞。
