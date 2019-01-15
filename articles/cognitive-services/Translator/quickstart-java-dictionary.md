---
title: 快速入門：在雙語字典中查詢單字 (Java) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何使用 Java 和翻譯工具文字 API 取得字詞的替代翻譯，以及這些替代翻譯的使用範例。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: 001aa2a813816562cdc5e95a00b858d8958a6756
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157814"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-java"></a>快速入門：使用 Java 在雙語字典中查詢單字

在本快速入門中，您將了解如何使用 Java 和翻譯工具文字 API 取得字詞的替代翻譯，以及這些替代翻譯的使用範例。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

* [JDK 7 或更新版本](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="initialize-a-project-with-gradle"></a>使用 Gradle 將專案初始化

首先，我們要建立此專案的工作目錄。 請從命令列 (或終端機) 執行下列命令：

```console
mkdir alt-translation-sample
cd alt-translation-sample
```

接著，您應初始化 Gradle 專案。 此命令會建立 Gradle 的基本組建檔案，最重要的是 `build.gradle.kts`，此檔案將在執行階段用來建立及設定您的應用程式。 從您的工作目錄執行下列命令：

```console
gradle init --type basic
```

出現選擇 **DSL** 的提示時，請選取 [Kotlin]。

## <a name="configure-the-build-file"></a>設定組建檔案

找出 `build.gradle.kts`，並使用您慣用的 IDE 或文字編輯器加以開啟。 然後，在此組建組態中複製下列內容：

```
plugins {
    java
    application
}
application {
    mainClassName = "AltTranslation"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

請注意，此範例中的 HTTP 要求會使用 OkHttp，且會使用 Gson 來處理及剖析 JSON。 如果您想要深入了解組建組態，請參閱[建立新的 Gradle 組建](https://guides.gradle.org/creating-new-gradle-builds/)。

## <a name="create-a-java-file"></a>建立 Java 檔案

我們將建立範例應用程式的資料夾。 請從您的工作目錄執行：

```console
mkdir -p src/main/java
```

接著，在此資料夾中建立名為 `AltTranslation.java` 的檔案。

## <a name="import-required-libraries"></a>匯入必要的程式庫

開啟 `AltTranslation.java` 並新增下列 Import 陳述式：

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>定義變數

首先，您必須建立專案的公用類別：

```java
public class AltTranslation {
  // All project code goes here...
}
```

將以下幾行新增至 `AltTranslation` 類別。 您會注意到，連同 `api-version` ，已將兩個額外參數附加到 `url`。 這些參數用來設定翻譯輸入和輸出。 在此範例中為英文 (`en`) 和西班牙文 (`es`)。

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es";
```

## <a name="create-a-client-and-build-a-request"></a>建立用戶端，並建置要求

將以下這一行新增至 `AltTranslation` 類別，以具現化 `OkHttpClient`：

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

接下來，我們將建置 POST 要求。 您可以視需要變更要翻譯的文字。

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Pineapples\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>建立用以剖析回應的函式

這個簡單的函式會剖析並美化翻譯工具文字服務的 JSON 回應。

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>組合在一起

最後一個步驟是提出要求，並取得回應。 將以下幾行新增至您的專案：

```java
public static void main(String[] args) {
    try {
        AltTranslation altTranslationRequest = new AltTranslation();
        String response = altTranslationRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>執行範例應用程式

就這樣，您準備要執行範例應用程式。 從命令列 (或終端機工作階段) 瀏覽至工作目錄的根目錄，並執行：

```console
gradle build
```

## <a name="sample-response"></a>範例回應

```json
[
  {
    "normalizedSource": "pineapples",
    "displaySource": "pineapples",
    "translations": [
      {
        "normalizedTarget": "piñas",
        "displayTarget": "piñas",
        "posTag": "NOUN",
        "confidence": 0.7016,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 5,
            "frequencyCount": 158
          },
          {
            "normalizedText": "cones",
            "displayText": "cones",
            "numExamples": 5,
            "frequencyCount": 13
          },
          {
            "normalizedText": "piña",
            "displayText": "piña",
            "numExamples": 3,
            "frequencyCount": 5
          },
          {
            "normalizedText": "ganks",
            "displayText": "ganks",
            "numExamples": 2,
            "frequencyCount": 3
          }
        ]
      },
      {
        "normalizedTarget": "ananás",
        "displayTarget": "ananás",
        "posTag": "NOUN",
        "confidence": 0.2984,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 2,
            "frequencyCount": 16
          }
        ]
      }
    ]
  }
]
```

## <a name="next-steps"></a>後續步驟

瀏覽本快速入門及其他文件的範例程式碼，包括翻譯和音譯，以及 GitHub 上的其他「翻譯工具文字」專案範例。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Java 範例](https://aka.ms/TranslatorGitHub?type=&language=java) (英文)

## <a name="see-also"></a>另請參閱

* [翻譯文字](quickstart-java-translate.md)
* [進行文字音譯](quickstart-java-transliterate.md)
* [從輸入識別語言](quickstart-java-detect.md)
* [取得支援的語言清單](quickstart-java-languages.md)
* [從輸入判斷句子長度](quickstart-java-sentences.md)
