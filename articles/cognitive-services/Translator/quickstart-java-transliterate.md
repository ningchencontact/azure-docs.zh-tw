---
title: 快速入門：轉換文字字集 (Java) - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您會了解如何搭配使用 Java 和翻譯工具文字 REST API，將文字從一個字集直譯 (轉換) 成另一個字集。 在此範例中，日文會直譯為使用拉丁字母。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: 2345204fc8b51cf1f7dc8020950c121e12676547
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961290"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-java"></a>快速入門：搭配使用翻譯工具文字 API 與 Java 進行文字音譯

在本快速入門中，您會了解如何搭配使用 Java 和翻譯工具文字 REST API，將文字從一個字集直譯 (轉換) 成另一個字集。 在所提供的範例中，日文會直譯為使用拉丁字母。

本快速入門需要 [Azure 認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)和翻譯工具文字資源。 如果您還沒有帳戶，可以使用[免費試用](https://azure.microsoft.com/try/cognitive-services/)來取得訂用帳戶金鑰。

## <a name="prerequisites"></a>必要條件

* [JDK 7 或更新版本](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* 翻譯工具文字的 Azure 訂用帳戶金鑰

## <a name="initialize-a-project-with-gradle"></a>使用 Gradle 將專案初始化

首先，我們要建立此專案的工作目錄。 請從命令列 (或終端機) 執行下列命令：

```console
mkdir transliterate-sample
cd transliterate-sample
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
    mainClassName = "Transliterate"
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

接著，在此資料夾中建立名為 `Transliterate.java` 的檔案。

## <a name="import-required-libraries"></a>匯入必要的程式庫

開啟 `Transliterate.java` 並新增下列 Import 陳述式：

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
public class Transliterate {
  // All project code goes here...
}
```

將以下幾行新增至 `Transliterate` 類別。 您會注意到，連同 `api-version` ，已將兩個額外參數附加到 `url`。 這些參數用來設定輸入語言，以及用於直譯的指令碼。 在此範例中，它會設為日文 (`jpan`) 和英文 (`latn`)。 請務必更新訂用帳戶金鑰值。

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```

## <a name="create-a-client-and-build-a-request"></a>建立用戶端，並建置要求

將以下這一行新增至 `Transliterate` 類別，以具現化 `OkHttpClient`：

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

接下來，我們將建置 POST 要求。 您可以視需要變更要直譯的文字。

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
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
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
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

當建置完成時，執行：

```console
gradle run
```

## <a name="sample-response"></a>範例回應

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>後續步驟

瀏覽本快速入門及其他文件的範例程式碼，包括翻譯和語言識別，以及 GitHub 上的其他「翻譯工具文字」專案範例。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Java 範例](https://aka.ms/TranslatorGitHub?type=&language=java) (英文)

## <a name="see-also"></a>另請參閱

* [翻譯文字](quickstart-java-translate.md)
* [從輸入識別語言](quickstart-java-detect.md)
* [取得替代的翻譯](quickstart-java-dictionary.md)
* [取得支援的語言清單](quickstart-java-languages.md)
* [從輸入判斷句子長度](quickstart-java-sentences.md)
