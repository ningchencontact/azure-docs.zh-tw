---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7ff04789a4ba5e5a689b3d3815852bc0fbcdc6a7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988313"
---
## <a name="register-your-application"></a>註冊您的應用程式

如接下來兩節所述，您可以在任一種方式註冊您的應用程式。

### <a name="option-1-express"></a>選項 1：快速

1. 移至 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)。
2. 在 [應用程式名稱] 中，輸入您應用程式的名稱。
3. 確認已選取 [引導式設定] 核取方塊，然後選取 [建立]。
4. 依照指示取得應用程式識別碼，然後將它貼到您的程式碼中。

### <a name="option-2-advanced"></a>選項 2：進階

1. 移至 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/portal/register-app)。
2. 在 [應用程式名稱] 方塊中，輸入應用程式的名稱。
3. 確認已清除 [引導式設定] 核取方塊，然後選取 [建立]。
4. 選取 [新增平台]，選取 [原生應用程式]，然後選取 [儲存]。
5. 在 [應用程式] > [Java] > [{host}.{namespace}] 之下，開啟 `MainActivity`。 
6. 使用您的應用程式/用戶端識別碼取代 *[在這裡輸入應用程式識別碼]*：

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. 在 [應用程式] > [資訊清單] 之下，開啟 AndroidManifest.xml 檔案。
8. 在 `manifest\application` 中，新增下列活動。 可讓 Microsoft 在完成驗證後回呼您應用程式的 `BrowserTabActivity` 活動：

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. 在 `BrowserTabActivity` 中，使用應用程式/用戶端識別碼取代 `[Enter the application Id here]`。
