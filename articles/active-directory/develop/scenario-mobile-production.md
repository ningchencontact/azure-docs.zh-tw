---
title: 呼叫 web Api （移至生產環境）-Microsoft 身分識別平台的行動裝置應用程式
description: 了解如何建置行動應用程式呼叫 web Api （移至生產環境）
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8b6a5c2a29228de806088ea93e197d42bf1ab47
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962347"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>行動裝置應用程式呼叫 web Api-移至生產環境

這篇文章提供有關如何改善品質和可靠性，您的應用程式之前將它移至生產環境的詳細資料。

## <a name="handling-errors-in-mobile-applications"></a>在 行動應用程式中處理錯誤

可以在此時應用程式中發生一些錯誤條件。 若要處理的主要案例是無訊息的失敗和後援至互動。 您的生產環境時，您應該考慮其他條件包括沒有網路的情況下，服務中斷、 系統管理員同意，需求和其他案例特定情況。

每個 MSAL 程式庫已說明如何處理這些狀況的範例程式碼 」 和 「 wiki 內容：

- [MSAL 的 Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL 的 iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>降低，並調查問題

若要診斷您的應用程式中的問題，最好先收集資料。 如需有關的資料類型資訊中，您可以收集，請參閱 MSAL 平台 wiki。

- 使用者可能會遇到問題時尋求協助。 最佳的作法是擷取和暫時儲存記錄檔，並提供使用者可以上傳它們的位置。 MSAL 會提供記錄延伸模組，以擷取驗證的詳細的資訊。
- 如果有的話，可讓遙測透過 MSAL 來收集有關使用者登入您的應用程式方式的資料。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
