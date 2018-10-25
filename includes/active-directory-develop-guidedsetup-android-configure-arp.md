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
ms.openlocfilehash: fe2b02b2495b4f37cbc90e1ddbeaca43b41d008c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843363"
---
## <a name="add-the-applications-registration-to-your-code"></a>將應用程式註冊資訊新增到您的程式碼

在這個步驟中，您需要將應用程式/用戶端識別碼新增到您的專案。

1.  開啟 `MainActivity` (在 `app` > `java` > *`{host}.{namespace}`* 底下)
2.  將開頭為 `final static String CLIENT_ID` 的那一行取代為：
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. 開啟：`app` > `manifests` > `AndroidManifest.xml`
4. 將下列活動新增到 `manifest\application`。 `BrowserTabActivity` 可讓 Microsoft 在完成驗證後回呼您的應用程式：

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
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

