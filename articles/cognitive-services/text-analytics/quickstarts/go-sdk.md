---
title: 快速入門：適用於 Go 的文字分析用戶端程式庫 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 取得資訊和程式碼範例，協助您在 Azure 認知服務中快速開始使用文字分析 API。
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 1ba2ec6b5c0c59be7b7264f7558fbb393adcc2d8
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142796"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>快速入門：適用於 Go 的文字分析用戶端程式庫

開始使用適用於 Go 的文字分析用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 

使用適用於 Go 的文字分析用戶端程式庫執行：

* 情感分析
* 語言偵測
* 實體辨識
* 關鍵片語擷取

[參考文件](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [套件 (Github)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Go](https://golang.org/dl/)

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>建立文字分析 Azure 資源 

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立文字分析的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services/#decision)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。
* 在 [Azure 入口網站](https://portal.azure.com)上檢視您的資源。

從試用版訂用帳戶或資源取得金鑰後，請為名為 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 的金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-go-project"></a>建立新的 Go 專案

在主控台視窗 (cmd、PowerShell、Bash) 中，為您的 Go 專案建立新的工作區，並瀏覽至該工作區。 您的工作區將包含三個資料夾： 

* **src** - 此目錄包含原始程式碼和套件。 任何使用 `go get` 命令安裝的套件都將位於此處。
* **pkg** - 此目錄包含已編譯的 Go 套件物件。 這些檔案都具有 `.a` 副檔名。
* **bin** - 此目錄包含您執行 `go install` 時所建立的二進位可執行檔。

> [!TIP]
> 深入了解 [Go 工作區](https://golang.org/doc/code.html#Workspaces)的結構。 本指南包含用來設定 `$GOPATH` 和 `$GOROOT` 的資訊。

建立名為 `my-app` 的工作區，以及 `src`、`pkg` 和 `bin` 所需的子目錄：

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>安裝適用於 Go 的文字分析用戶端程式庫

安裝適用於 Go 的用戶端程式庫： 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

或者，如果您使用 dep，請在存放庫中執行：

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>建立您的 Go 應用程式

接著，建立名為 `src/quickstart.go` 的檔案：

```bash
$ cd src
$ touch quickstart.go
```

在您慣用的 IDE 或文字編輯器中開啟 `quickstart.go`。 然後，新增套件名稱，並匯入下列程式庫：

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"    
)
```

請將下列函式新增至專案，因為此快速入門預期大部分的參數和屬性都是字串和 bool 指標。

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

在您應用程式的 `main` 函式中，為資源的 Azure 端點和訂用帳戶金鑰建立變數。 從環境變數 TEXT_ANALYTICS_SUBSCRIPTION_KEY 和 TEXT_ANALYTICS_ENDPOINT 取得這些值。 如果您在開始編輯應用程式後才建立這些環境變數，則必須先關閉您使用的編輯器、IDE 或殼層，再重新加以開啟，才能存取該變數。

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if "" == os.Getenv(subscriptionKeyVar) {
    log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
}
var subscriptionKey string = os.Getenv(subscriptionKeyVar)
var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
if "" == os.Getenv(endpointVar) {
    log.Fatal("Please set/export the environment variable " + endpointVar + ".")
}
var endpoint string = os.Getenv(endpointVar)
```

## <a name="object-model"></a>物件模型 

文字分析用戶端是一個 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 物件，會使用您的金鑰向 Azure 進行驗證。 此用戶端提供數種用來分析文字 (以單一字串或批次的形式) 的方法。 

文字會以 `documents` 清單的形式傳送至 API，而此清單中列載 `id`、`text` 和 `language` 屬性的組合 (視使用的方法而定) 所屬的 `dictionary` 物件。 `text` 屬性會儲存要在原始 `language` 中分析的文字，而 `id` 可以是任何值。 

回應物件是一份清單，包含每個文件的分析資訊。 

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的文字分析用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [情感分析](#sentiment-analysis)
* [語言偵測](#language-detection)
* [實體辨識](#entity-recognition)
* [關鍵片語擷取](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>驗證用戶端

在您專案的 main 函式中，建立新的 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 物件。 將您的金鑰傳至 [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) 函式，繼而傳至用戶端的 `authorizer` 屬性。

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>情感分析

建立名為 `SentimentAnalysis()` 的新函式，取用稍早建立的用戶端。 建立 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 物件的清單，其中包含您要分析的文件。 每個物件會包含 `id`、`Language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

在相同的函式中，呼叫用戶端的 [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) 函式，並取得結果。 接著逐一查看結果，並列印每個文件的識別碼和人氣分數。 接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

```golang
result, err := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s\n", *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

在您專案的 main 函式中，呼叫 `SentimentAnalysis()`。

### <a name="output"></a>輸出

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>語言偵測

建立名為 `LanguageDetection()` 的新函式，取用稍早建立的用戶端。 建立 [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) 物件的清單，其中包含您要分析的文件。 每個物件會包含 `id` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，而 `id` 可以是任何值。 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

在相同的函式中，呼叫用戶端的 [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage)，並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼和偵測到的語言。

```golang
result, err := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

在您專案的 main 函式中，呼叫 `LanguageDetection()`。

### <a name="output"></a>輸出

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>實體辨識

建立名為 `ExtractEntities()` 的新函式，取用稍早建立的用戶端。 建立 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 物件的清單，其中包含您要分析的文件。 每個物件會包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。 

```golang
func ExtractEntities(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

在相同的函式中，呼叫用戶端的 [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities)，並取得結果。 接著逐一查看結果，並輸出每個文件的識別碼以及擷取的實體分數。

```golang
    result, err := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

在您專案的 main 函式中，呼叫 `ExtractEntities()`。

### <a name="output"></a>輸出

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>關鍵片語擷取

建立名為 `ExtractKeyPhrases()` 的新函式，取用稍早建立的用戶端。 建立 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 物件的清單，其中包含您要分析的文件。 每個物件會包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

在相同的函式中，呼叫用戶端的 [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases)，並取得結果。 然後逐一查看結果，並輸出每個文件的識別碼以及擷取的主要片語。

```golang
    result, err := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

在您專案的 main 函式中，呼叫 `ExtractKeyPhrases()`。

### <a name="output"></a>輸出

```console
Document ID: 0
    Extracted Key Phrases:
        cat
        veterinarian
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除與資源群組相關聯的任何其他資源。

* [入口網站](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟


> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [文字分析概觀](../overview.md)
* [情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [實體辨識](../how-tos/text-analytics-how-to-entity-linking.md)
* [偵測語言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [辨識語言](../how-tos/text-analytics-how-to-language-detection.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices) 上找到。
