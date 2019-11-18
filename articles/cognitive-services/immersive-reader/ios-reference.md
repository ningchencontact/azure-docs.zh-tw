---
title: 沉浸式讀取器 iOS SDK 參考
titleSuffix: Azure Cognitive Services
description: 沉浸式讀取器 iOS SDK 是一個 Swift CocoaPod，可讓您將沉浸式讀者整合到您的 iOS 應用程式。
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 67d6b8c22c5635bd789078a7f91b02f8b07e5e70
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903130"
---
# <a name="immersive-reader-sdk-reference-for-ios"></a>適用于 iOS 的沉浸式讀取器 SDK 參考

沉浸式讀取器 iOS SDK 是一個 Swift CocoaPod，可讓您將沉浸式讀者整合到您的 iOS 應用程式。

## <a name="functions"></a>函式

SDK 會公開單一函式 `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`。

### <a name="launchimmersivereader"></a>launchImmersiveReader

藉由在您的 iOS 應用程式中啟動視圖控制器，啟動沉浸式讀取器。

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>parameters

| 名稱 | 在系統提示您進行確認時，輸入 | 描述 |
| ---- | ---- |------------ |
| `navController` | UINavigationController | 要從中呼叫函式之 iOS 應用程式的流覽控制器。 |
| `token` | 字串 | Azure AD 驗證 token。 請參閱[Azure AD authentication](./azure-active-directory-authentication.md)的作法。 |
| `subdomain` | 字串 | Azure 中您的沉浸式讀取器資源的自訂子域。 請參閱[Azure AD authentication](./azure-active-directory-authentication.md)的作法。 |
| `content` | [內容](#content) | 物件，包含要在沉浸式讀取器中顯示的內容。 |
| `options` | [選項](#options) | 設定沉浸式讀取器特定行為的選項。 選用。 |
| `onSuccess` | （）-> Void | 當沉浸式讀取器成功啟動時，所叫用的關閉。 |
| `onFailure` | （_ 錯誤：[錯誤](#error)）-> Void | 當沉浸式讀取器無法載入時，所叫用的關閉。 這個關閉會傳回一個[`Error`](#error)物件，代表與失敗相關聯的錯誤碼和錯誤訊息。 如需詳細資訊，請參閱[錯誤碼](#error-codes)。 |

## <a name="types"></a>類型

### <a name="content"></a>內容

包含要顯示在沉浸式讀取器中的內容。

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>支援的 MIME 類型

| MIME 類型 | 描述 |
| --------- | ----------- |
| text/plain | 純文字。 |
| application/mathml + xml | 數學標記語言（MathML）。 [詳細資訊](https://developer.mozilla.org/en-US/docs/Web/MathML)。

### <a name="options"></a>選項

包含屬性，可設定沉浸式讀取器的特定行為。

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>錯誤

包含錯誤的相關資訊。

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>錯誤碼

| 代碼 | 描述 |
| ---- | ----------- |
| BadArgument | 提供的引數無效。如需詳細資料，請參閱 `message`。 |
| 逾時 | 沉浸式讀取器無法在指定的超時時間內載入。 |
| TokenExpired | 提供的權杖已過期。 |
| 受 | 已超過呼叫率限制。 |
| InternalError | 沉浸式讀取器視圖控制器內發生內部錯誤。 如需詳細資訊，請參閱 `message`。|

## <a name="os-version-support"></a>作業系統版本支援

在 iPad 和 iPhone 上，支援 iOS 9.0 或更新版本的沉浸式讀取器 iOS SDK。

## <a name="next-steps"></a>後續步驟

* 探索[GitHub 上的沉浸式讀取器 IOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [快速入門：建立可啟動沉浸式讀取器（Swift）的 iOS 應用程式](./ios-quickstart.md)