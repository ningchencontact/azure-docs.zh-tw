---
title: 快速入門：取得支援的語言 (Ruby) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您可以取得使用翻譯工具文字 API 搭配 Ruby 來翻譯、音譯及查閱字典時，所支援的語言清單與範例。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: erhopf
ms.openlocfilehash: 1b92adcf528a1ccd00983e6c0dd952fefab5dd7d
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2019
ms.locfileid: "55975461"
---
# <a name="quickstart-get-supported-languages-with-the-translator-text-rest-api-ruby"></a>快速入門：使用翻譯工具文字 REST API (Ruby) 取得支援的語言

在本快速入門中，您可以取得使用翻譯工具文字 API 來翻譯、音譯及查閱字典時，所支援的語言清單及範例。

## <a name="prerequisites"></a>必要條件

您將需要有 [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) (英文) 或更新版本，才能執行此程式碼。

## <a name="languages-request"></a>Languages 要求

下列程式碼會使用 [Languages](./reference/v3-0-languages.md) 方法取得在翻譯、音譯及查詢字典時，所支援的語言清單及範例。

1. 在您慣用的程式碼編輯器中，建立新的 Ruby 專案。
2. 新增下方提供的程式碼。
3. 執行程式。

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'

uri = URI (host + path)

request = Net::HTTP::Get.new(uri)

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))

output_path = 'output.txt'

# Write response to file
File.open(output_path, 'w' ) do |output|
    output.print json
end
```

## <a name="languages-response"></a>Languages 回應

成功的回應會以 JSON 格式傳回，如下列範例所示：

```json
{
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
  },
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
  }
}
```

## <a name="next-steps"></a>後續步驟

瀏覽本快速入門及其他文件的範例程式碼，包括翻譯和音譯，以及 GitHub 上的其他「翻譯工具文字」專案範例。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Ruby 範例](https://aka.ms/TranslatorGitHub?type=&language=ruby) (英文)
