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
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544431"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android 專用 MSAL.NET 考量
使用系統瀏覽器上 Xamarin Android 使用 Microsoft Authentication Library for.NET (MSAL.NET) 時，這篇文章會討論的特定考量。

開始 MSAL.NET 2.4.0-preview，MSAL.NET 支援 Chrome 以外的瀏覽器，並不再需要使用 Chrome 安裝在 Android 裝置以進行驗證。

我們建議使用支援自訂的索引標籤，這類的瀏覽器：

| 自訂索引標籤支援的瀏覽器 | 封裝名稱 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|奇異果 | com.kiwibrowser.browser|
|勇敢 | com.brave.browser|

除了自訂索引標籤的支援，根據我們的測試中，瀏覽器不支援自訂索引標籤的幾個瀏覽器也適用於驗證：Opera、 Opera Mini InBrowser 和 Maxthon。 如需詳細資訊，請參閱[測試結果的資料表](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)。

## <a name="known-issues"></a>已知問題

- 如果使用者不可以在裝置上啟用任何瀏覽器，將會擲回 MSAL.NET`AndroidActivityNotFound`例外狀況。 
  - **降低風險**：通知使用者他們應該啟用瀏覽器 （最好是一個自訂索引標籤支援） 在其裝置上。

- 如果驗證失敗 （例如。 驗證會隨即啟動並 DuckDuckGo），將會傳回 MSAL.NET `AuthenticationCanceled MsalClientException`。 
  - **根問題**:自訂索引標籤支援的瀏覽器未啟用裝置上。 驗證啟動替代的瀏覽器，無法完成驗證。 
  - **降低風險**：通知使用者他們應該在其裝置上安裝瀏覽器 （最好是一個自訂索引標籤支援）。

## <a name="devices-and-browsers-tested"></a>裝置和瀏覽器測試
下表列出的裝置和瀏覽器已經過測試。

| | 瀏覽器&ast;     |  結果  | 
| ------------- |:-------------:|:-----:|
| 華為一個 + | Chrome&ast; | 通過|
| 華為一個 + | Edge&ast; | 通過|
| 華為一個 + | Firefox&ast; | 通過|
| 華為一個 + | 勇敢&ast; | 通過|
| 一個 + | Ecosia&ast; | 通過|
| 一個 + | Kiwi&ast; | 通過|
| 華為一個 + | Opera | 通過|
| Huawei | OperaMini | 通過|
| 華為一個 + | InBrowser | 通過|
| 一個 + | Maxthon | 通過|
| 華為一個 + | DuckDuckGo | 使用者已取消驗證|
| 華為一個 + | UC 瀏覽器 | 使用者已取消驗證|
| 一個 + | 隻海豚以 | 使用者已取消驗證|
| 一個 + | CM 瀏覽器 | 使用者已取消驗證|
| 華為一個 + | 沒有任何已安裝 | AndroidActivityNotFound ex|

&ast; 支援自訂索引標籤

## <a name="next-steps"></a>後續步驟
程式碼片段和其他有關系統瀏覽器使用 Xamarin Android，請閱讀本[指南](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  