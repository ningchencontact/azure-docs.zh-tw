---
title: 與 App Service Mobile Apps 整合
description: 了解如何搭配使用 Azure 通知中樞和 Azure App Service Mobile Apps。
author: jwargo
manager: patniko
editor: spelluru
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
ms.author: jowargo
ms.openlocfilehash: bb39949bc21ece819b7ac6e279390f8bdb0ee00a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157446"
---
# <a name="integration-with-app-service-mobile-apps"></a>與 App Service Mobile Apps 整合

> [!NOTE]
> Microsoft 致力於完全支援 Azure App Service Mobile Apps，包括最新的 OS 版本、錯誤修正、文件改善，以及社群 PR 檢閱。 請注意，產品小組目前並未投入製作任何 Azure Mobile Apps 的新功能。 我們非常感謝社群在 Azure Mobile Apps 所有領域的貢獻。

為了在 Azure 服務間促進完美且統一的體驗， [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) 具備使用通知中樞提供推播通知的內建支援。 [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) 具有高擴充性且可供全球使用，是專為企業開發人員與系統整合人員設計的行動應用程式開發平台，能提供一組豐富的功能給行動應用程式開發人員。

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
