---
title: Xamarin iOS 考慮 (適用于 .NET 的 Microsoft 驗證程式庫) |Azure
description: 瞭解使用 Xamarin iOS 搭配適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 時的特定考慮。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 906f2fc8cdac31922e6e93526f65577fe76c4b9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532394"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>MSAL.NET 的 Xamarin iOS 特定考慮
在 Xamarin iOS 上, 當您使用 MSAL.NET 時, 必須考慮幾個事項

- [IOS 12 和驗證的已知問題](#known-issues-with-ios-12-and-authentication)
- [覆寫並在`OpenUrl`中執行函數`AppDelegate`](#implement-openurl)
- [啟用 Keychain 群組](#enable-keychain-access)
- [啟用權杖快取共用](#enable-token-cache-sharing-across-ios-applications)
- [啟用 Keychain 存取](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 和驗證的已知問題
Microsoft 已發行[安全性諮詢](https://github.com/aspnet/AspNetCore/issues/4647), 以提供 iOS12 與某些驗證類型之間不相容的相關資訊。 不相容會中斷社交、WSFed 和 OIDC 登入。 這項諮詢也會提供開發人員如何將 ASP.NET 新增至其應用程式的最新安全性限制, 以使其與 iOS12 相容的指引。  

在 Xamarin iOS 上開發 MSAL.NET 應用程式時, 您可能會在嘗試從 iOS 12 登入網站時看到無限迴圈 (類似于此[ADAL 問題](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329))。 

您也可能會在使用 iOS 12 Safari ASP.NET Core OIDC authentication 時看到中斷, 如此[WebKit 問題](https://bugs.webkit.org/show_bug.cgi?id=188165)中所述。

## <a name="implement-openurl"></a>執行 OpenUrl

首先, 您必須覆寫`OpenUrl` `FormsApplicationDelegate`衍生類別的方法, 並呼叫`AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`。

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

您也必須定義 URL 配置、需要應用程式呼叫其他應用程式的許可權、具有特定的重新導向 URL 形式, 並在[Azure 入口網站](https://portal.azure.com)中註冊此重新導向 url。

### <a name="enable-keychain-access"></a>啟用 keychain 存取

若要啟用 keychain 存取, 您的應用程式必須具有 keychain 存取群組。
建立應用程式時, 您可以使用`WithIosKeychainSecurityGroup()` api 來設定 keychain 存取群組, 如下所示:

若要啟用單一登入, 您必須在所有應用程式`PublicClientApplication.iOSKeychainSecurityGroup`中將屬性設定為相同的值。

使用 MSAL v3. x 的範例如下:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Plist 應該更新為類似下列 XML 片段:

這項變更是*除了*使用下列存取群組或您`Entitlements.plist`自己的檔案, 在檔案中啟用 keychain 存取權以外:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

使用 MSAL v4. x 的範例如下:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

使用`WithIosKeychainSecurityGroup()` api 時, MSAL 會自動將您的安全性群組附加至應用程式的「小組識別碼」 (AppIdentifierPrefix) 的結尾, 因為當您使用 xcode 來建立應用程式時, 它會執行相同的動作。 [如需詳細資訊, 請參閱 iOS 權利檔](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。 這就是為什麼您需要在 plist 中的 keychain 存取群組之前, 更新權利以包含 $ (AppIdentifierPrefix)。

### <a name="enable-token-cache-sharing-across-ios-applications"></a>啟用跨 iOS 應用程式的權杖快取共用

在 MSAL 2.x 中, 您可以指定 Keychain 存取群組, 以用於保存多個應用程式的權杖快取。 此設定可讓您在具有相同 keychain 存取群組的數個應用程式之間共用權杖快取, 包括使用[ADAL.NET](https://aka.ms/adal-net)、MSAL.NET Xamarin 應用程式和使用[開發的原生 iOS 應用程式所開發的Objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc)或[MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。

共用權杖快取可讓使用相同 Keychain 存取群組的所有應用程式之間進行單一登入。

若要啟用此快取共用, 您必須設定使用 ' WithIosKeychainSecurityGroup () ' 方法, 將 keychain 存取群組設定為所有共用相同快取之應用程式中的相同值, 如上述範例所示。

先前已提到, 每當您使用`WithIosKeychainSecurityGroup()` api 時, MSAL 會新增 $ (AppIdentifierPrefix)。 這是因為 AppIdentifierPrefix 或「小組識別碼」是用來確保只有相同發行者所進行的應用程式可以共用 keychain 存取權。

#### <a name="note-keychainsecuritygroup-property-deprecated"></a>注意:KeychainSecurityGroup 屬性已被取代

先前, 從 MSAL 2.x 開始, 開發人員在使用`KeychainSecurityGroup`屬性時, 強制包含 TeamId 前置詞

從 MSAL 2.7. x 開始, 使用新`iOSKeychainSecurityGroup`的屬性時, MSAL 將會在執行時間解析 TeamId 前置詞。 使用此屬性時, 此值不應包含 TeamId 前置詞。

使用新`iOSKeychainSecurityGroup`的屬性, 這不需要開發人員提供 TeamId, 因為上一個`KeychainSecurityGroup`屬性現在已經過時。

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>說明 Xamarin iOS 特定屬性的範例

如需更多詳細資料, 請在下列範例 readme.md 檔案的[IOS 特定考慮](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations)段落中提供:

樣本 | 平台 | 描述
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 簡單的 Xamarin Forms 應用程式展示如何使用 MSAL 透過 Azure AD v2.0 端點來驗證 MSA 和 Azure AD, 並使用產生的權杖來存取 Microsoft Graph。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
