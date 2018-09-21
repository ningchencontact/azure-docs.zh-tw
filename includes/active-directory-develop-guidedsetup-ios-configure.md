---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: brandwe
manager: mtillman
ms.openlocfilehash: ca7a47fbe2c5ee2a4eb10abf3b9b50a2d28c252e
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466154"
---
## <a name="register-your-application"></a>註冊您的應用程式
如接下來兩節所述，您可以在任一種方式註冊您的應用程式。

### <a name="option-1-express-mode"></a>選項 1：快速模式
現在您需要在「Microsoft 應用程式註冊入口網站」註冊應用程式：
1. 透過 [Microsoft 應用程式註冊入口網站 (英文)](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure) 註冊您的應用程式
2.  輸入應用程式的名稱和您的電子郵件
3.  確定已選取 [Guided Setup (引導式設定)] 選項
4.  依照指示取得應用程式識別碼並貼到您的程式碼中

### <a name="option-2-advanced-mode"></a>選項 2：進階模式

1.  移至 [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/portal/register-app)
2.  輸入應用程式的名稱
3.  確定已取消選取 [Guided Setup (引導式設定)] 選項
4.  按一下 [`Add Platform`]，然後選取 [`Native Application`] 並按一下 [`Save`]
5.  返回至 Xcode。 在 `ViewController.swift` 中，以您剛剛註冊的應用程式識別碼取代開頭為 '`let kClientID`' 的行：

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
按住 Ctrl 鍵並按一下 <code>Info.plist</code> 以顯示內容功能表，然後按一下：<code>Open As</code> > <code>Source Code</code>
</li>
<li>
在 <code>dict</code> 根節點下，新增下列項目：
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
用您剛剛註冊的應用程式識別碼取代 <i><code>[Your_Application_Id_Here]</code></i>
</li>
</ol>
