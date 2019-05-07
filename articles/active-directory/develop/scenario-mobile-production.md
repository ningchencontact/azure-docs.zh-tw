---
title: 呼叫 web Api （移至生產環境）-Microsoft 身分識別平台的行動裝置應用程式
description: 了解如何建置行動應用程式呼叫 Web Api （移至生產環境）
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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37d2de561a6f5841bf17a47fef86ad7639750d5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074946"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>行動裝置應用程式呼叫 web Api-移至生產環境

這篇文章提供改善的品質和可靠性，您的應用程式移到生產環境的詳細資料。

## <a name="handling-errors-in-mobile-applications"></a>在 行動應用程式中處理錯誤

在不同的流程，我們已反白顯示到目前為止，有各種不同的原因會造成的錯誤狀況。 無訊息的失敗和後援，以互動的主要案例來處理。 有額外的條件，您也應該考慮包括沒有網路的情況下，服務中斷，有需要，系統管理員同意和其他案例特定情況下的生產環境。

每個 MSAL 程式庫已探討到處理這些狀況的範例程式碼 」 和 「 wiki 內容。

- [MSAL 的 Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL 的 iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>降低，並調查問題

收集資料，可協助您診斷問題的應用程式。 如需詳細資訊的資料類型中，您可以收集，請參閱每個 MSAL 平台 wiki。

- 發生問題時，使用者可能會尋求協助。 最佳的作法是擷取和記錄檔暫時儲存，並允許使用者上傳它們某處。 MSAL 會提供記錄來擷取驗證相關的詳細的資訊的延伸模組
- 如果有的話，可讓遙測透過 MSAL 來收集有關使用者如何登入您的應用程式的資料。

## <a name="testing-your-app"></a>測試應用程式

請務必測試您的應用程式，針對[整合檢查清單](identity-platform-integration-checklist.md)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
