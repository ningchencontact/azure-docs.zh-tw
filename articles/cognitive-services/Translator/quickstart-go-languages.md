---
title: 快速入門：取得支援的語言 (Go) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您可以取得搭配 Go 使用翻譯工具文字 API 來翻譯、音譯及查閱字典時，所支援的語言清單與範例。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: erhopf
ms.openlocfilehash: 1e630d4dee3629fc256fdc97eefad259aff909e2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000276"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-go"></a>快速入門：搭配使用翻譯工具文字 API 與 Go 來取得支援的語言清單

在本快速入門中，您將了解如何搭配使用 Go 與翻譯工具文字 REST API 來發出 GET 要求，以傳回支援的語言清單。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Go](https://golang.org/doc/install)
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="create-a-project-and-import-required-modules"></a>建立專案，並匯入所需的模組

使用您慣用的 IDE 或編輯器建立新的 Go 專案。 然後，將下列程式碼片段複製到您的專案中名為 `get-languages.go` 的檔案。

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>建立 main 函式

此範例會嘗試從環境變數 `TRANSLATOR_TEXT_KEY` 中讀取您的翻譯工具文字訂用帳戶金鑰。 如果您不熟悉環境變數，您可以將 `subscriptionKey` 設為字串，並註解化條件陳述式。

請將下列程式碼複製到您的專案中：

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our getLanguages function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    getLanguages(subscriptionKey)
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>建立函式以取得支援的語言清單

讓我們來建立函式以取得支援的語言清單。 此函式將會接受單一引數：您的翻譯工具文字訂用帳戶金鑰。

```go
func getLanguages(subscriptionKey string) {
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
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/languages?api-version=3.0")
q := u.Query()
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
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

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

從 GitHub 上的 [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) (英文)，探索認知服務 API 的 Go 套件。

> [!div class="nextstepaction"]
> [探索 GitHub 上的 Go 套件](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices) (英文)

## <a name="see-also"></a>另請參閱

了解如何使用翻譯工具文字 API 來執行下列動作：

* [翻譯文字](quickstart-go-translate.md)
* [進行文字音譯](quickstart-go-transliterate.md)
* [從輸入識別語言](quickstart-go-detect.md)
* [取得替代的翻譯](quickstart-go-dictionary.md)
* [從輸入判斷句子長度](quickstart-go-sentences.md)
