---
title: 如何指定辨識模型-臉部 API
titleSuffix: Azure Cognitive Services
description: 本文將說明如何選擇哪一個辨識模型，要讓 Azure 人臉識別 API 應用程式使用。
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: e8d5c416183a7d475a46c5e538577069612baf8e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449016"
---
# <a name="specify-a-face-recognition-model"></a>指定臉部辨識模式

本指南將說明如何指定臉部偵測、 識別和相似度搜尋服務中使用 Azure 人臉識別 API 的臉部辨識模型。

人臉識別 API 會使用機器學習服務模型上執行作業的人臉映像中。 我們持續改善我們根據客戶的意見反應和研究的進展的模型的精確度，我們提供這些改進為模型更新。 開發人員可以選擇指定臉部辨識模型的版本他們想要使用;他們可以選擇最適合他們的使用案例的模型。

如果您是新的使用者，我們建議使用最新的模型。 繼續閱讀以了解如何在不同的臉部作業中加以指定，同時還能避免模型衝突。 如果您是進階的使用者，並不確定是否應該切換至最新的模型，請跳至[評估不同的模型](#evaluate-different-models)區段來評估新的模型，並使用您目前的資料集的結果進行比較。

## <a name="prerequisites"></a>必要條件

您應該熟悉如何使用 AI 臉部偵測及識別的概念。 如果您不是，請先查閱這些操作方法指南：

* [如何偵測影像中的臉孔](HowtoDetectFacesinImage.md)
* [如何找出影像中的臉孔](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>偵測到與指定之模型的臉部

臉部偵測會識別人臉的重要視覺道具，並尋找其週框方塊的位置。 它也會擷取臉部的功能，並將其儲存以用於識別。 所有的這項資訊會形成一個臉部的表示法。

辨識模型時使用臉部功能會擷取，以便執行偵測作業時，您可以指定模型版本。

使用時[Face - Detect]API，將指定的模型版本`recognitionModel`參數。 可用的值為：

* `recognition_01`
* `recognition_02`

您可以選擇性地指定_returnRecognitionModel_參數 (預設**false**)，表示是否_recognitionModel_應該在回應中傳回。 因此，要求 URL[Face - Detect]REST API 看起來像這樣：

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]
&subscription-key=<Subscription key>`

如果您使用用戶端程式庫，您可以指派的值`recognitionModel`藉由傳遞字串，表示版本。
如果您將它保留未指定，預設的模型版本 (_recognition_01_) 會使用。 請參閱下列程式碼範例.NET 用戶端程式庫。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>使用指定的模型識別人臉

人臉識別 API 可以擷取映像中的臉部資料，以及其關聯**Person**物件 (透過[新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)API 呼叫，例如)，和多重**人員**物件可以是一起儲存在**PersonGroup**。 然後，可以針對比較新的面貌**PersonGroup** (使用[面臨-識別]呼叫)，而且可以識別該群組內的比對的人員。

A **PersonGroup**應該有一個唯一辨識模型的所有**人員**，然後您可以使用來指定這`recognitionModel`參數，當您建立的群組 ([PersonGroup - 建立]或是[LargePersonGroup - 建立])。 如果您未指定這個參數時，原始`recognition_01`使用模型。 群組一律會使用辨識模型建立，並新增給它; 時，新的表面會變成此模型與相關聯無法在群組的建立之後變更。 若要查看哪些模型**PersonGroup**設定，使用[PersonGroup - Get]搭配使用 API _returnRecognitionModel_參數設定為**true**.

請參閱下列程式碼範例.NET 用戶端程式庫。

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

在此程式碼中， **PersonGroup**識別碼`mypersongroupid`建立，而且它設定為使用_recognition_02_模型，以擷取臉部特徵。

同樣地，您需要指定哪一個模型来使用偵測臉部時，用來比較這**PersonGroup** (透過[Face - Detect]API)。 您使用的模型應該一律是配合**PersonGroup**的組態; 否則作業會失敗因不相容的模型。

在沒有變更[面臨-識別]API，您只需要在偵測指定的模型版本。

## <a name="find-similar-faces-with-specified-model"></a>尋找具有指定之模型相似臉部

您也可以指定相似度搜尋的辨識模型。 您可以將指派的模型版本`recognitionModel`建立具有臉部清單時[FaceList-建立]API 或[LargeFaceList - Create]。 如果您未指定這個參數時，原始`recognition_01`使用模型。 臉部清單一律會使用辨識模型建立，並新增給它; 時，新的表面會變成此模型與相關聯無法在建立後變更。 若要查看臉部清單已使用哪一種模型，請使用[FaceList - Get]搭配使用 API _returnRecognitionModel_參數設定為 **，則為 true**。

請參閱下列程式碼範例.NET 用戶端程式庫。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

此程式碼會建立名為臉部清單`My face collection`，並使用_recognition_02_模型來擷取特徵。 當您搜尋新偵測到臉部的相似臉部此臉部清單時，該臉部必須偵測到 ([Face - Detect]) 使用_recognition_02_模型。 上一節中，模型必須保持一致。

在沒有變更[臉部-尋找相似]API，您只能在偵測指定的模型版本。

## <a name="verify-faces-with-specified-model"></a>驗證具有指定之模型的臉部

[面臨-驗證]API 會檢查是否兩張臉部是屬於同一個人。 不沒有關於辨識模型中，確認 API 中的任何變更，但您只可以比較臉部偵測到使用相同的模型。 因此，這兩張臉兩者必須偵測到使用`recognition_01`或`recognition_02`。

## <a name="evaluate-different-models"></a>評估不同的模型

如果您想要比較的效能_recognition_01_並_recognition_02_對您的資料模型，您必須：

1. 建立兩個**PersonGroup**向_recognition_01_並_recognition_02_分別。
1. 偵測的臉部，並註冊以使用您的映像資料**人員**這兩個 s **PersonGroup**，以及觸發程序訓練和處理[PersonGroup - 定型]API。
1. 使用測試[面臨-識別]兩者**PersonGroup**s，並比較結果。

如果您通常會指定信心臨界值 （介於 0 到決定不太安心模型必須是識別臉部的其中一個值），您可能需要針對不同的模型使用不同的臨界值。 一個模型的臨界值不是共用到另一個，一定不會產生相同的結果。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何指定辨識模型，以搭配不同臉部服務 Api。 接下來，請依照下列快速入門來開始使用臉部偵測。

* [偵測影像中的臉部](../quickstarts/csharp-detect-sdk.md)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[臉部-尋找相似]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[面臨-識別]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[面臨-驗證]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - 定型]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList-建立]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
