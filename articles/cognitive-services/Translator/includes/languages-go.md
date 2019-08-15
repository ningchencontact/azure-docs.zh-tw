---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 707e6c1fb063ca7c8580df4ace2685417fd7847d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968334"
---
## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Go](https://golang.org/doc/install)

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

使用您最愛的 IDE 或編輯器，或在您桌面上的新資料夾中，建立新的 Go 專案。 然後，將下列程式碼片段複製到您的專案/資料夾中名為 `get-languages.go` 的檔案。

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)
```

## <a name="create-the-main-function"></a>建立 main 函式

讓我們為應用程式建立主要函式。 您會發現這是一行程式碼。 這是因為我們正在建立單一函式，以取得並列出 Translator Text 的支援語言清單。

請將下列程式碼複製到您的專案中：

```go
func main() {
    /*
     * This calls our getLanguages function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    getLanguages()
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>建立函式以取得支援的語言清單

讓我們來建立函式以取得支援的語言清單。

```go
func getLanguages() {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

接下來，讓我們來建構 URL。 使用 `Parse()` 和 `Query()` 方法來建置 URL。

將此程式碼複製到 `getLanguages` 函式。

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/languages")
q := u.Query()
q.Add("api-version", "3.0")
u.RawQuery = q.Encode()
```

>[!NOTE]
> 如需關於端點、路由和要求參數的詳細資訊，請參閱[翻譯工具文字 API 3.0：語言](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)。

## <a name="build-the-request"></a>建立要求

既然您已將要求本文編碼為 JSON，您就可以建置 POST 要求並呼叫翻譯工具文字 API。

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

如果您使用認知服務的多服務訂用帳戶，您也必須在要求參數中包含 `Ocp-Apim-Subscription-Region`。 [深入了解使用多服務訂用帳戶進行驗證](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="handle-and-print-the-response"></a>處理及列印回應

將此程式碼新增到 `getLanguages` 函式以將 JSON 回應解碼，然後將結果格式化並加以列印。

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>組合在一起

如此，您就建立了一個簡單的程式，會呼叫翻譯工具文字 API 並傳回 JSON 回應。 現在，請執行您的程式：

```console
go run get-languages.go
```

如果想要將您的程式碼與我們的做比較，請使用 [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go) 上提供的完整範例。

## <a name="sample-response"></a>範例回應

請在此[語言清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中尋找國家/地區縮寫。

成功的回應會以 JSON 格式傳回，如下列範例所示：

```json
{
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  },
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>後續步驟

查看 API 參考，以了解您可以使用翻譯工具文字 API 執行的所有作業。

> [!div class="nextstepaction"]
> [API 參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
