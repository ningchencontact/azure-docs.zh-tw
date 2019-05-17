---
title: Xamarin Android 考量 (Microsoft Authentication Library for.NET) |Azure
description: 使用 Xamarin Android 使用 Microsoft Authentication Library for.NET (MSAL.NET) 時，請了解特定的考量。
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
ms.openlocfilehash: cb0cfb06e95cadbb549f669e5d59bdb0d795c896
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545868"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android 專用 MSAL.NET 考量
使用 Xamarin Android 使用 Microsoft Authentication Library for.NET (MSAL.NET) 時，這篇文章會討論的特定考量。

本文適用於 MSAL.NET 3.x。 如果您有興趣使用 MSAL.NET 2.x，請參閱[MSAL.NET 中的 Xamarin Android 細節 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x)。

## <a name="set-the-parent-activity"></a>設定父活動

在 Xamarin.Android 中，您需要設定父系活動，使權杖，取得上一步之後發生的互動時，才。

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>確保控制項就會回到 MSAL 一次的驗證流程結束互動式的部分
在 Android 上，您需要覆寫`OnActivityResult`方法的`Activity`和呼叫 SetAuthenticationContinuationEventArgs AuthenticationContinuationHelper MSAL 類別方法。

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
這一行可確保，控制項就會回到 MSAL 一旦驗證流程的互動部分結束。

## <a name="update-the-android-manifest"></a>更新 Android 資訊清單
`AndroidManifest.xml`應該包含下列值：
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>使用內嵌的 web 檢視 （選擇性）

根據預設 MSAL.NET 會使用系統 web 瀏覽器，可讓您取得與 Web 應用程式的 SSO 與其他應用程式。 在某些罕見的情況下，您可能想要指定您想要使用內嵌的 web 檢視。 如需詳細資訊，請參閱 < [MSAL.NET 使用網頁瀏覽器](msal-net-web-browsers.md)並[Android 系統瀏覽器](msal-net-system-browser-android-considerations.md)。

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>疑難排解
如果您建立新的 Xamarin.Forms 應用程式，並新增 MSAL.Net NuGet 套件的參考，這也能夠運作。
不過，如果您想要升級 MSAL.NET 現有的 Xamarin.Forms 應用程式預覽 1.1.2 或更新版本，您可能會遇到組建問題。

若要疑難排解這些問題，您應該：
- 將現有的 MSAL.NET NuGet 套件更新 MSAL.NET 預覽 1.1.2 或更新版本
- 檢查 Xamarin.Forms，自動更新為版本 2.5.0.122203 （如果沒有，此版本的更新）
- 檢查 Xamarin.Android.Support.v4，自動更新為版本 25.4.0.2 （如果沒有，此版本的更新）
- Xamarin.Android.Support 的所有套件應該以版本為都目標 25.4.0.2
- 清除/重建
- 請嘗試將最大平行專案建置到 Visual Studio 中為 1 （選項]-> [專案和方案]-> [建置及執行平行專案組建的最大數目]-> [）
- 或者，如果您要從命令列建置，請嘗試移除 /m 在命令中，如果您使用。


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Error:'AuthenticationContinuationHelper' 不存在於目前的內容名稱

這可能是因為 Visual Studio 無法正確更新 Android.csproj* 檔案。 有時候**<HintPath>** 檔案路徑不正確地包含而不是 netstandard13 **monoandroid90**。

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>後續步驟

中提供更多詳細資料和範例[Android 的特定考量](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)段落下面的範例的 readme.md 檔案：

| 範例 | 平台 | 說明 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 展示如何使用 MSAL 來驗證 MSA 與 Azure AD 透過實例化 v2.0 端點，並存取 Microsoft Graph 與產生的語彙基元的簡單 Xamarin Forms 應用程式。 <br>![拓撲](media/msal-net-xamarin-android-considerations/topology.png) |