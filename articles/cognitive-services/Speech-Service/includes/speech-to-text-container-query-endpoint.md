---
title: 查詢語音轉換文字容器端點
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: a55cf5ea6aa696d0cf0cdd619dc22839d748d83c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491099"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>語音轉換文字或自訂語音轉換文字

容器提供以 websocket 為基礎的查詢端點 Api，可透過[語音 SDK](../index.md)存取。 根據預設，語音 SDK 會使用線上語音服務。 若要使用容器，您必須變更初始化方法。

> [!TIP]
> 搭配使用語音 SDK 與容器時，您不需要提供 Azure 語音資源訂用帳戶[金鑰或驗證持有人權杖](../rest-speech-to-text.md#authentication)。

請參閱以下範例。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

從使用此 Azure 雲端初始化呼叫變更：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

使用容器[端點](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet)進行此呼叫：

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

從使用此 Azure 雲端初始化呼叫變更：

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

使用容器[端點](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)進行此呼叫：

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

***
