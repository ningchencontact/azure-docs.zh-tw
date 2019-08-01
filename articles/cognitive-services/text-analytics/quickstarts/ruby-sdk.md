---
title: 快速入門：使用 Ruby SDK 來呼叫文字分析認知服務
titleSuffix: Azure Cognitive Services
description: 取得資訊和程式碼範例，協助您在 Azure 認知服務中快速開始使用文字分析 API。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 3f18b77fe436328e79df351b9c5edcf6dc289ad7
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697274"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>快速入門：使用 Ruby SDK 來呼叫文字分析服務

<a name="HOLTop"></a>


透過此快速入門，開始使用 Text Analytics SDK for Ruby 來分析語言。 雖然 [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API 與大部分程式設計語言相容，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb) 上找到。

如需 API 的技術文件，請參閱 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>必要條件

* [Ruby 2.5.5 或更新版本](https://www.ruby-lang.org/)
* [適用於 Ruby 的文字分析 SDK](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>建立 Ruby 專案並安裝 SDK

1. 建立新的 Ruby 專案，並新增名為 `Gemfile` 的新檔案。
2. 藉由在 `Gemfile` 中新增以下程式碼，於專案中新增文字分析 SDK。

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>建立文字分析用戶端

1. 在您慣用的編輯器或 IDE 中，建立名為 `TextAnalyticsExamples.rb` 的新檔案。 匯入文字分析 SDK。

2. 文字分析用戶端會使用認證物件。 請使用 `CognitiveServicesCredentials.new()` 並傳遞訂用帳戶金鑰來建立此物件。

3. 使用正確的文字分析端點建立用戶端。

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>情感分析

您可以使用文字分析 SDK 或 API，對一組文字記錄執行情感分析。 下列範例會顯示數個文件的情感分數。

1. 建立稱為 `SentimentAnalysisExample()` 的新函式，以採用上面所建立的文字分析用戶端來作為參數。

2. 定義一組要分析的 `MultiLanguageInput` 物件。 為每個物件新增語言和文字。 識別碼可以是任何值。

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. 在同一個函式內，將文件結合到清單中。 將其新增至 `MultiLanguageBatchInput` 物件的 `documents` 欄位。 

4. 使用 `MultiLanguageBatchInput` 物件作為參數來呼叫用戶端的 `sentiment()` 函式，以傳送文件。 如果傳回任何結果，請加以印出。
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. 呼叫 `SentimentAnalysisExample()` 函式。

    ```ruby
    SentimentAnalysisExample(textAnalyticsClient)
    ```

### <a name="output"></a>輸出

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>語言偵測

文字分析服務可針對大量的語言和地區設定，偵測某個文字文件的語言。 下列範例會顯示用來撰寫數個文件的語言。

1. 建立稱為 `DetectLanguageExample()` 的新函式，以採用上面所建立的文字分析用戶端來作為參數。 

2. 定義一組要分析的 `LanguageInput` 物件。 為每個物件新增語言和文字。 識別碼可以是任何值。

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. 在同一個函式內，將文件結合到清單中。 將其新增至 `LanguageBatchInput` 物件的 `documents` 欄位。 

4. 使用 `LanguageBatchInput` 物件作為參數來呼叫用戶端的 `detect_language()` 函式，以傳送文件。 如果傳回任何結果，請加以印出。
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. 呼叫 `DetectLanguageExample` 函式

    ```ruby
    DetectLanguageExample(textAnalyticsClient)
    ```

### <a name="output"></a>輸出

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>實體辨識

文字分析服務可以區分並擷取文字文件中的不同實體 (人員、地點和事物)。 下列範例會顯示在數個文件範例中找到的實體。

1. 建立稱為 `Recognize_Entities()` 的新函式，以採用上面所建立的文字分析用戶端來作為參數。

2. 定義一組要分析的 `MultiLanguageInput` 物件。 為每個物件新增語言和文字。 識別碼可以是任何值。

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. 在同一個函式內，將文件結合到清單中。 將其新增至 `MultiLanguageBatchInput` 物件的 `documents` 欄位。 

4. 使用 `MultiLanguageBatchInput` 物件作為參數來呼叫用戶端的 `entities()` 函式，以傳送文件。 如果傳回任何結果，請加以印出。

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. 呼叫 `RecognizeEntitiesExample` 函式
    ```ruby
    RecognizeEntitiesExample(textAnalyticsClient)
    ```

### <a name="output"></a>輸出

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>關鍵片語擷取

文字分析服務可以擷取句子中的關鍵片語。 下列範例會顯示在多種語言的數個文件範例中找到的實體。

1. 建立稱為 `KeyPhraseExtractionExample()` 的新函式，以採用上面所建立的文字分析用戶端來作為參數。

2. 定義一組要分析的 `MultiLanguageInput` 物件。 為每個物件新增語言和文字。 識別碼可以是任何值。

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. 在同一個函式內，將文件結合到清單中。 將其新增至 `MultiLanguageBatchInput` 物件的 `documents` 欄位。 

4. 使用 `MultiLanguageBatchInput` 物件作為參數來呼叫用戶端的 `key_phrases()` 函式，以傳送文件。 如果傳回任何結果，請加以印出。

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. 呼叫 `KeyPhraseExtractionExample` 函式

    ```ruby
    KeyPhraseExtractionExample(textAnalyticsClient)
    ```

### <a name="output"></a>輸出

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另請參閱

 [文字分析概觀](../overview.md)  
 [常見問題集 (FAQ)](../text-analytics-resource-faq.md)
