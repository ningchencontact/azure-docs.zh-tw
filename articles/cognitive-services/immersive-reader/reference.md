---
title: 沈浸式讀取器 SDK 參考
titleSuffix: Azure Cognitive Services
description: 沈浸式的讀取器 SDK 的參考
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 485e8626af4266492e02d4f9fbe4af486e10c082
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718399"
---
# <a name="immersive-reader-sdk-reference"></a>沈浸式讀取器 SDK 參考

JavaScript 程式庫，可讓您整合至 web 應用程式中的沈浸式的讀取器沈浸式的讀取器 SDK。

## <a name="functions"></a>函式

SDK 會公開單一的函式， `ImmersiveReader.launchAsync(token, resourceName, content, options)`。

### <a name="launchasync"></a>launchAsync

啟動沈浸式的讀取器內`iframe`web 應用程式中。

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>參數

| 名稱 | type | 說明 |
| ---- | ---- |------------ |
| `token` | string | 取得從呼叫的存取權杖`issueToken`端點。 |
| `resourceName` | string | 保留。 必須設為 `null`。 |
| `content` | [內容](#content) | 物件，包含要在沈浸式的讀取器中顯示的內容。 |
| `options` | [選項](#options) | 設定沈浸式的讀取器的特定行為的選項。 選擇性。 |

#### <a name="returns"></a>傳回值

傳回`Promise<HTMLDivElement>`解析沈浸式的讀取器載入時。 `Promise`解析成`div`項目是其唯一的子系`iframe`包含沈浸式的讀取器 頁面的項目。

#### <a name="exceptions"></a>例外狀況

傳回`Promise`拒絕[ `Error` ](#error)物件如果沈浸式的讀取器，就無法載入。 如需詳細資訊，請參閱 <<c0> [ 錯誤碼](#error-codes)。

## <a name="types"></a>類型

### <a name="content"></a>內容

包含要在沈浸式的讀取器中顯示的內容。

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
| text/plain | 純文字。 |
| application/mathml+xml | 數學標記語言 (MathML)。 [深入了解](https://developer.mozilla.org/en-US/docs/Web/MathML)。

### <a name="options"></a>選項。

包含設定沈浸式的讀取器的特定行為的屬性。

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Error

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
| BadArgument | 提供的引數無效，請參閱`message`如需詳細資訊。 |
| 逾時 | 沈浸式的讀取器無法載入指定的逾時期限內。 |
| TokenExpired| 提供的權杖已過期。 |

## <a name="launching-the-immersive-reader"></a>啟動沈浸式的讀取器

SDK 會提供啟動沈浸式的讀取器的按鈕的預設樣式。 使用`immersive-reader-button`類別屬性，若要啟用此樣式。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>選擇性的屬性

您可以使用下列屬性來設定按鈕的外觀與風格。

| 屬性 | 說明 |
| --------- | ----------- |
| `data-button-style` | 設定按鈕的樣式。 可以是 `icon`、`text` 或 `iconAndText`。 預設值為 `icon`。 |
| `data-locale` | 設定地區設定，例如`en-US`， `fr-FR`。 預設為英文。 |
| `data-icon-px-size` | 設定圖示的大小，單位為像素。 預設為 20px。 |

## <a name="browser-support"></a>瀏覽器支援

使用以下瀏覽器的最新版本的沈浸式的讀取器的最佳體驗。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>後續步驟

* 瀏覽[沈浸式讀取器在 GitHub 上的 SDK](https://github.com/Microsoft/immersive-reader-sdk)
* [快速入門：建立 web 應用程式啟動沈浸式的讀取器 (C#)](./quickstart.md)