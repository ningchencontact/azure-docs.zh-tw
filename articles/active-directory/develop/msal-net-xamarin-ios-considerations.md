---
title: Xamarin iOS 考量 (Microsoft Authentication Library for.NET) |Azure
description: 使用 Xamarin iOS 使用 Microsoft Authentication Library for.NET (MSAL.NET) 時，請了解特定的考量。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf236bff2300129ec97d3b8946c4c2a2748bca77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602142"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Xamarin iOS 專屬 MSAL.NET 考量
在 Xamarin iOS 中，有幾個使用 MSAL.NET 時，您必須考慮到的考量

- [IOS 12 和驗證的已知的問題](#known-issues-with-ios-12-and-authentication)
- [覆寫並實作`OpenUrl`函式中 `AppDelegate`](#implement-openurl)
- [啟用 Keychain 群組](#enable-keychain-groups)
- [啟用權杖快取共用](#enable-token-cache-sharing-across-ios-applications)
- [啟用金鑰鏈存取](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 和驗證的已知的問題
Microsoft 已發行[資訊安全摘要報告](https://github.com/aspnet/AspNetCore/issues/4647)提供 iOS12 與某些類型的驗證之間的不相容性的相關資訊。 不相容性符號社交、 WSFed 和 OIDC 登入。 本忠告也會提供有關開發人員可執行以移除目前的安全性限制，由 ASP.NET 新增至他們的應用程式變得與 iOS12 相容的指引。  

在開發 Xamarin iOS 上的 MSAL.NET 應用程式時，嘗試從 iOS 12 登入網站時，您可能會看到無限迴圈 (如下所示[ADAL 問題](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)。 

您也可能會看到使用 iOS 12 的 ASP.NET Core OIDC 驗證中斷在此所述的 Safari [WebKit 問題](https://bugs.webkit.org/show_bug.cgi?id=188165)。

## <a name="implement-openurl"></a>實作 OpenUrl

首先您必須覆寫`OpenUrl`方法`FormsApplicationDelegate`衍生類別並呼叫`AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`。

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

您也需要定義 URL 配置，而需要呼叫另一個應用程式、 特定形式的重新導向 URL，然後註冊在此重新導向 URL 的應用程式的權限[Azure 入口網站](https://portal.azure.com)。

## <a name="enable-keychain-groups"></a>啟用 KeyChain 群組

若要讓權杖快取的工作，並有`AcquireTokenSilentAsync`方法的工作，必須有多個步驟：
1. 啟用金鑰鏈存取在您*`* Entitlements.plist* 檔案，並指定**Keychain 群組**中套件組合識別碼。
2. 選取  *`*Entitlements.plist*`* 中的檔案**自訂權利**iOS 專案選項 視窗中的欄位**套件組合簽署 檢視**。
3. XCode 時簽署憑證，請確定會使用相同的 Apple id。

## <a name="enable-token-cache-sharing-across-ios-applications"></a>啟用 iOS 應用程式之間共用的權杖快取

從 MSAL 2.x 中，您可以指定要用來保存跨多個應用程式的權杖快取 keychain 安全性群組。 這可讓您分享幾個具有相同金鑰鏈安全性群組，包括開發的應用程式之間的權杖快取[ADAL.NET](https://aka.ms/adal-net)，MSAL.NET Xamarin.iOS 應用程式和原生 iOS 應用程式開發具有[ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc)或是[MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。

共用權杖快取可讓單一登入 (SSO) 之間的所有使用相同的金鑰鏈安全性群組應用程式。

若要讓單一登入，您需要設定`PublicClientApplication.iOSKeychainSecurityGroup`中所有的應用程式的相同值的屬性。

這個範例使用 MSAL v3.x 會是：
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

這個範例使用 MSAL v2.7.x 會是：

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> `KeychainSecurityGroup`屬性已被取代。 先前在 MSAL 2.x 中，開發人員被迫使用時，包含 TeamId 前置詞`KeychainSecurityGroup`屬性。 
> 
> 現在，從 MSAL 2.7.x，MSAL 會解析此 TeamId 前置詞在執行階段時使用`iOSKeychainSecurityGroup`屬性。 當使用這個屬性的值不能包含 TeamId 前置詞。 
> 
> 使用新`iOSKeychainSecurityGroup`屬性，不需要開發人員提供 TeamId。 `KeychainSecurityGroup`屬性現已過時。 

## <a name="enable-keychain-access"></a>啟用金鑰鏈存取

Msal 2.x 和 ADAL 4.x TeamId 用來存取可讓單一登入 (SSO) 的相同發行者的應用程式之間提供驗證程式庫的金鑰鏈。 

何謂[TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)？ 它是 App Store 中的唯一識別碼 （公司或個人）。 AppId 是唯一的應用程式。 如果您有多個應用程式，TeamId 的所有應用程式將會相同，但 AppId 會不同。 Keychain 存取群組加上 TeamId 自動為每個群組的系統。 它是作業系統強制來自相同發行者的應用程式可以存取的共用金鑰鏈。 

初始化時`PublicClientApplication`，如果您收到`MsalClientException`訊息： `TeamId returned null from the iOS keychain...`，您必須執行 iOS 的 Xamarin 應用程式中的下列：

1. 在 VS 中偵錯 索引標籤下前往 nameOfMyApp.iOS 屬性...
2. 然後移至 iOS 套件組合簽署 
3. 在 自訂權利下按一下 ...從您的應用程式中選取 Entitlements.plist 檔案
4. 在 csproj 檔案中的 iOS 應用程式，您應該有現在包含此行： `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **重建**專案。

這是*此外*若要啟用金鑰鏈存取中的`Entitlements.plist`檔案，使用以下存取群組或您自己：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>後續步驟

中提供更多詳細資料[iOS 的特定考量](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations)段落下面的範例的 readme.md 檔案：

範例 | 平台 | 說明 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 展示如何使用 MSAL 來驗證 MSA 與 Azure AD 透過 AAD V2.0 端點，並存取 Microsoft Graph 與產生的語彙基元的簡單 Xamarin Forms 應用程式。 <br>![拓撲](media/msal-net-xamarin-ios-considerations/topology.png)