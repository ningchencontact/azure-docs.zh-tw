---
title: 呼叫 web Api 的行動應用程式 (移至生產環境)-Microsoft 身分識別平臺
description: 瞭解如何建立會呼叫 web Api 的行動應用程式 (移至生產環境)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e778e55bd87e325b3dcf14a8c9f1616157a420b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320880"
---
# <a name="mobile-app-that-calls-web-apis---move-to-production"></a>呼叫 web Api 的行動應用程式-移至生產環境

本文提供如何在將應用程式移至生產環境之前, 先改善其品質和可靠性的詳細資料。

## <a name="handling-errors-in-mobile-applications"></a>處理行動應用程式中的錯誤

此時, 您的應用程式可能會發生一些錯誤狀況。 要處理的主要案例是無訊息失敗, 並會回到互動。 您應該考慮用於生產環境的其他條件包括: 無網路狀況、服務中斷、系統管理員同意需求, 以及其他案例特定的情況。

每個 MSAL 程式庫都有範例程式碼和 wiki 內容, 說明如何處理這些條件:

- [MSAL Android Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigating-and-investigating-issues"></a>緩和和調查問題

若要診斷應用程式中的問題, 它有助於收集資料。 如需您可以收集之資料類型的相關資訊, 請參閱 MSAL platform wiki。

- 當使用者遇到問題時, 可能會要求您提供協助。 最佳做法是捕捉並暫時儲存記錄, 並提供可讓使用者上傳的位置。 MSAL 提供記錄延伸模組來捕獲有關驗證的詳細資訊。
- 如果可用, 請透過 MSAL 啟用遙測, 以收集使用者如何登入您的應用程式的相關資料。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
