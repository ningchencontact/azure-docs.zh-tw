---
title: 非同步對話轉譯（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音服務來進行非同步對話轉譯。 僅適用于 JAVA。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500878"
---
# <a name="asynchronous-conversation-transcription-preview"></a>非同步對話轉譯（預覽）

在本文中，非同步對話轉譯會使用**RemoteConversationTranscriptionClient** API 來示範。 如果您已設定對話轉譯來執行非同步轉譯，並具有 `conversationId`，您可以使用**RemoteConversationTranscriptionClient** API 來取得與該 `conversationId` 相關聯的轉譯。

## <a name="asynchronous-vs-real-time--asynchronous"></a>非同步與即時 + 非同步

透過非同步轉譯，您可以串流處理對話音訊，但不需要即時傳回的轉譯。 相反地，在傳送音訊之後，請使用 `ConversationTranscriber` 的 `conversationId` 來查詢非同步轉譯的狀態。 當非同步轉譯準備就緒時，您會收到 `RemoteConversationTranscriptionResult`。

有了即時加上非同步功能，您可以即時取得轉譯，但也會藉由使用 `conversationId` 來查詢來取得轉譯（類似于非同步案例）。

完成非同步轉譯需要兩個步驟。 第一個步驟是上傳音訊，選擇 [僅限非同步] 或 [即時加上非同步]。 第二個步驟是取得轉譯結果。

## <a name="upload-the-audio"></a>上傳音訊

在執行非同步轉譯之前，您必須使用 Microsoft 認知語音用戶端 SDK （1.8.0 或更新版本）將音訊傳送至對話轉譯。

此範例程式碼示範如何針對非同步模式建立對話 transcriber。 若要將音訊串流至 transcriber，您必須[使用語音 SDK 來即時](how-to-use-conversation-transcription-service.md)新增衍生自轉譯交談的音訊串流程式碼。 請參閱該主題的**限制**一節，以查看支援的平臺和語言 api。

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
...
```

如果您想要即時_加上_非同步，請將適當的程式程式碼批註並取消批註，如下所示：

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>取得轉譯結果

此步驟會取得非同步轉譯結果，但會假設您可能需要的任何即時處理都是在其他地方完成。 如需詳細資訊，請參閱[使用語音 SDK 即時轉譯對話](how-to-use-conversation-transcription-service.md)。

針對此處顯示的程式碼，您需要**remoteconversation-client-sdk 1.0.0 版**，僅支援 Windows、Linux 和 Android 上的 JAVA （1.8 或更新版本）（API 層級26或以上）。

### <a name="obtaining-the-client-sdk"></a>取得用戶端 SDK

您可以藉由編輯 pom .xml 檔案來取得**remoteconversation-用戶端 sdk** ，如下所示。

- 在檔案結尾的結束記號 `</project>`之前，請先建立一個 `repositories` 元素，其中包含適用于語音 SDK 的 Maven 存放庫參考：

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- 此外，使用 remoteconversation-client-sdk 1.0.0 作為相依性來新增 `dependencies` 元素：

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- 儲存變更

### <a name="sample-transcription-code"></a>範例轉譯程式碼

在您擁有 `conversationId`之後，請在用戶端建立遠端操作物件**RemoteConversationTranscriptionOperation** ，以查詢非同步轉譯的狀態。 **RemoteConversationTranscriptionOperation**是從[輪詢](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java)器延伸的。 完成輪詢之後，請訂閱輪詢器並查詢物件，以取得**RemoteConversationTranscriptionResult** 。 在此程式碼中，我們只會將結果內容列印到系統輸出。

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
                                    System.out.println(result.getOffset());
                                    System.out.println(result.getDuration());
                                    System.out.println(result.getUserId());
                                    System.out.println(result.getReason());
                                    System.out.println(result.getResultId());
                                    System.out.println(result.getText());
                                    System.out.println(result.toString());
                                }
                            }
                        }
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)
