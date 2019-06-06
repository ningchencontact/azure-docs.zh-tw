---
title: 如何指定偵測模型-臉部 API
titleSuffix: Azure Cognitive Services
description: 本文將說明如何選擇哪一個臉部偵測模型，以使用您的 Azure 人臉識別 API 應用程式。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: dde5623bf5bd579a13fa7271dfba64f9df61bad1
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576718"
---
# <a name="specify-a-face-detection-model"></a>指定臉部偵測模型

本指南將說明如何為 Azure 人臉識別 API 指定臉部偵測模型。

人臉識別 API 會使用機器學習服務模型上執行作業的人臉映像中。 我們持續改善我們根據客戶的意見反應和研究的進展的模型的精確度，我們提供這些改進為模型更新。 開發人員可以選擇指定的臉部偵測模型版本他們想要使用;他們可以選擇最適合他們的使用案例的模型。

繼續閱讀以了解如何在特定臉部操作中指定的臉部偵測模型。 人臉識別 API 會使用臉部偵測，每當它將臉部的映像轉換成其他形式的資料。

如果您不確定您是否應使用最新的模型，請跳至[評估不同的模型](#evaluate-different-models)區段來評估新的模型，並使用您目前的資料集的結果進行比較。

## <a name="prerequisites"></a>必要條件

您應該熟悉如何使用 AI 臉部偵測的概念。 如果您不是，請參閱臉部偵測的概念指南或作法指南：

* [臉部偵測的概念](../concepts/face-detection.md)
* [如何偵測影像中的臉孔](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>偵測到與指定之模型的臉部

臉部偵測尋找個人類臉部的週框方塊位置，並識別其視覺化的地標。 它會擷取臉部的功能，並將其儲存供稍後用於[辨識](../concepts/face-recognition.md)作業。

當您使用[Face - Detect]API，您可以將指派的模型版本`detectionModel`參數。 可用的值為：

* `detection_01`
* `detection_02`

要求 URL[Face - Detect]REST API 看起來像這樣：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]
&subscription-key=<Subscription key>`

如果您使用用戶端程式庫，您可以指派的值`detectionModel`藉由傳入適當的字串。 如果您讓未指派，API 會使用預設的模型版本 (`detection_01`)。 請參閱下列程式碼範例.NET 用戶端程式庫。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>使用指定的模型加入個人的臉部

人臉識別 API 可以擷取映像中的臉部資料，以及其關聯**Person**物件傳遞[PersonGroup 個人新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API。 在此 API 呼叫時，您可以指定偵測模型中的相同方式[Face - Detect]。

請參閱下列程式碼範例.NET 用戶端程式庫。

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceServiceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceServiceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

此程式碼會建立**PersonGroup**識別碼`mypersongroupid`，並將**人員**給它。 然後它會新增這表面**Person**使用`detection_02`模型。 如果您未指定*detectionModel*參數，此 API 會使用預設的模型， `detection_01`。

> [!NOTE]
> 您不需要使用相同的偵測模型中所有臉部的**Person**物件，而您不需要用來比較以相同的偵測模型時偵測新的表面**人員**物件 (在[面臨-識別]API，例如)。

## <a name="add-face-to-facelist-with-specified-model"></a>使用指定的模型中加入 FaceList 臉部

您也可以指定偵測模型，當您新增至現有的表面**FaceList**物件。 請參閱下列程式碼範例.NET 用戶端程式庫。

```csharp
await faceServiceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

此程式碼會建立**FaceList**稱為`My face collection`並將它與臉部`detection_02`模型。 如果您未指定*detectionModel*參數，此 API 會使用預設的模型， `detection_01`。

> [!NOTE]
> 您不需要使用相同的偵測模型中所有臉部**FaceList**物件，而您不需要用來比較以相同的偵測模型時偵測新的表面**FaceList**物件。

## <a name="evaluate-different-models"></a>評估不同的模型

不同的臉部偵測模型最適合用於不同的工作。 請參閱下表中的差異的概觀。

|**detection_01**  |**detection_02**  |
|---------|---------|
|臉部偵測的所有作業的預設選項。 | 發行 2019 年，可選擇性地在所有臉部偵測作業。
|不適合小型、 側邊檢視表或模糊的臉部。  | 改善的小型、 側邊檢視和模糊的臉部的精確度。 |
|如果它們偵測呼叫中指定，傳回將臉部屬性 （標頭的姿勢、 年齡、 表情，等等）。 |  不會傳回臉部屬性。     |
|傳回會面臨地標，如果它們偵測呼叫中指定。   | 不會傳回臉部特徵。  |

若要比較的效能，最好`detection_01`和`detection_02`模型是範例資料集上使用它們。 我們建議您呼叫[Face - Detect]API，透過各種不同的映像，特別是映像的多種面向或臉部很難看到，請使用每個偵測模型。 請注意每個模型傳回的臉部數目。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何指定偵測模型，以使用不同的臉部 Api。 接下來，請依照下列快速入門來開始使用臉部偵測。

* [偵測映像 (.NET SDK) 中的臉孔](../quickstarts/csharp-detect-sdk.md)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[面臨-識別]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc