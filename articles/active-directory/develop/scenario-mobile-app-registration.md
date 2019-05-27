---
title: 呼叫 web Api-應用程式的程式碼設定的行動裝置應用程式 |Microsoft 身分識別平台
description: 了解如何建置行動應用程式呼叫 web Api （應用程式的程式碼組態）
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
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962399"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>呼叫 web Api-應用程式註冊的行動裝置應用程式

這篇文章包含用於建立行動應用程式的應用程式註冊指示。

## <a name="supported-account-types"></a>支援的帳戶類型

支援行動裝置應用程式的帳戶類型取決於您想要啟用的體驗和您的應用程式為目標的使用者。

## <a name="platform-configuration-and-redirect-uris"></a>平台設定和重新導向 Uri  

建置行動應用程式，最重要的註冊步驟時的重新導向 URI。 這可以透過設定[在 [驗證] 刀鋒視窗中的平台組態](https://aka.ms/MobileAppReg)。

這項體驗可讓您的應用程式通過單一登入 (SSO) 的 Microsoft Authenticator 和 （在 Android 上的 Intune 公司入口網站） 以及支援裝置管理原則。

如果您想要手動設定 重新導向 URI，則可以透過應用程式資訊清單。 建議的格式如下：

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Android 的簽章雜湊就可以使用透過 KeyTool 命令發行或偵錯金鑰產生。

## <a name="api-permissions"></a>API 權限

行動應用程式代表登入的使用者呼叫 Api。 您的應用程式必須要求委派的權限，也稱為領域。 根據所需的體驗，做法是以靜態方式在 Azure 入口網站或以動態方式在執行階段。 靜態註冊權限可讓系統管理員輕鬆地核准您的應用程式，建議使用。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得權杖](scenario-mobile-acquire-token.md)
