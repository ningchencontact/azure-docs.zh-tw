---
title: 沉浸式讀取器 SDK 參考
titleSuffix: Azure Cognitive Services
description: 沉浸式讀取器 SDK 的參考
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 22860e0798ec852b6d25da27e108befe8a3089a2
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488821"
---
# <a name="immersive-reader-sdk-reference"></a>沉浸式讀取器 SDK 參考

沉浸式讀取器 SDK 是一個 JavaScript 程式庫, 可讓您將沉浸式讀取器整合到您的 web 應用程式中。

## <a name="functions"></a>Functions

SDK 會公開單一`ImmersiveReader.launchAsync(token, subdomain, content, options)`函式。

### <a name="launchasync"></a>launchAsync

`iframe`在您的 web 應用程式中, 啟動中的沉浸式讀取器。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>參數

| 名稱 | Type | 描述 |
| ---- | ---- |------------ |
| `token` | string | Azure AD 驗證 token。 請參閱[Azure AD authentication](./azure-active-directory-authentication.md)的作法。 |
| `subdomain` | string | Azure 中您的沉浸式讀取器資源的自訂子域。 請參閱[Azure AD authentication](./azure-active-directory-authentication.md)的作法。 |
| `content` | [內容](#content) | 物件, 包含要在沉浸式讀取器中顯示的內容。 |
| `options` | [選項](#options) | 設定沉浸式讀取器特定行為的選項。 選擇性。 |

#### <a name="returns"></a>傳回值

`Promise<HTMLDivElement>`傳回可在載入沉浸式讀取器時解析的。 會解析為元素, 其唯一的子系`iframe`是包含沉浸式讀取器頁面的專案。 `div` `Promise`

#### <a name="exceptions"></a>例外狀況

如果無法`Promise`載入沉浸式讀取器[`Error`](#error) , 則傳回的將會被物件拒絕。 如需詳細資訊, 請參閱[錯誤碼](#error-codes)。

## <a name="types"></a>類型

### <a name="content"></a>內容

包含要顯示在沉浸式讀取器中的內容。

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>支援的 MIME 類型

| MIME 類型 | 描述 |
| --------- | ----------- |
| 文字/純文字 | 純文字。 |
| application/mathml+xml | 數學標記語言 (MathML)。 [深入了解](https://developer.mozilla.org/en-US/docs/Web/MathML)。

### <a name="options"></a>選項。

包含屬性, 可設定沉浸式讀取器的特定行為。

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>錯誤

包含錯誤的相關資訊。

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>錯誤碼

| 程式碼 | 描述 |
| ---- | ----------- |
| BadArgument | 提供的引數無效。 `message`如需詳細資料, 請參閱。 |
| 等候逾時 | 沉浸式讀取器無法在指定的超時時間內載入。 |
| TokenExpired| 提供的權杖已過期。 |

## <a name="launching-the-immersive-reader"></a>啟動沉浸式讀取器

SDK 會為啟動沉浸式讀取器的按鈕提供預設樣式。 `immersive-reader-button`使用類別屬性來啟用此樣式。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>選擇性屬性

使用下列屬性來設定按鈕的外觀與風格。

| 屬性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 設定按鈕的樣式。 可以是 `icon`、`text` 或 `iconAndText`。 預設值為 `icon`。 |
| `data-locale` | 設定地區設定, 例如`en-US`。 `fr-FR` 預設為英文。 |
| `data-icon-px-size` | 設定圖示的大小 (以圖元為單位)。 預設為20px。 |

## <a name="browser-support"></a>瀏覽器支援

使用下列瀏覽器的最新版本, 取得沉浸式讀取器的最佳體驗。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>後續步驟

* 探索 [GitHub 上的沈浸式閱讀程式 SDK](https://github.com/Microsoft/immersive-reader-sdk)
* [快速入門：建立可啟動沉浸式讀取器的 web 應用C#程式 ()](./quickstart.md)