---
title: 呼叫 web Api 的行動應用程式-應用程式的程式碼設定 |Microsoft 身分識別平臺
description: 瞭解如何建立呼叫 web Api 的行動應用程式 (應用程式的程式碼設定)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326114"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>呼叫 web Api 的行動應用程式-應用程式註冊

本文包含用來建立行動應用程式的應用程式註冊指示。

## <a name="supported-account-types"></a>支援的帳戶類型

行動應用程式中支援的帳戶類型取決於您想要啟用的體驗, 以及應用程式的目標使用者。

## <a name="platform-configuration-and-redirect-uris"></a>平臺設定和重新導向 Uri  

建立行動應用程式時, 最重要的註冊步驟是 [重新導向 URI]。 您可以透過[[驗證](https://aka.ms/MobileAppReg)] 分頁中的 [平臺設定] 來設定此選項。

此體驗可讓您的應用程式透過 Microsoft Authenticator (和 Android 上的 Intune 公司入口網站) 取得單一登入 (SSO), 並支援裝置管理原則。

如果您想要手動設定重新導向 URI, 您可以透過應用程式資訊清單來執行此動作。 建議的格式如下:

- ***iOS***:`msauth.<BUNDLE_ID>://auth`
- ***Android***:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - 您可以透過 KeyTool 命令, 使用 release 或 debug 金鑰來產生 Android 簽名雜湊。

## <a name="api-permissions"></a>API 權限

行動應用程式代表已登入的使用者呼叫 Api。 您的應用程式需要要求委派的許可權, 也稱為範圍。 視所需的經驗而定, 這可以透過 Azure 入口網站以靜態方式完成, 或在執行時間以動態方式執行。 靜態註冊許可權可讓系統管理員輕鬆地核准您的應用程式, 並建議您這麼做。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得權杖](scenario-mobile-acquire-token.md)
