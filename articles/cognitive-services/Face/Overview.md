---
title: 什麼是臉部 API 服務？
titleSuffix: Azure Cognitive Services
description: 此詞彙說明使用臉部 API 服務時可能遇到的字詞。
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 15de899be5ab85e9fe84ba1b6284bc9419fcf8a1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123465"
---
# <a name="what-is-the-face-api-service"></a>什麼是臉部 API 服務？

歡迎使用臉部 API 服務，這是提供更進階臉部演算法的雲端式服務。 臉部 API 包含兩個主要功能：使用屬性進行臉部偵測，以及臉部辨識。

## <a name="face-detection"></a>臉部偵測

臉部 API 在每個影像中，最多可偵測 64 張具有高精確度臉部位置的人臉。 而該影像可依檔案 (以位元組為單位) 或有效的 URL 來指定。

![概觀 - 臉部偵測](./Images/Face.detection.jpg)

表示影像中臉部位置的臉部矩形 (左方、上方、寬度和高度) 會和每個偵測到的臉部一起傳回。 臉部偵測也可以擷取一連串與臉部相關的屬性，例如姿勢、性別、年齡、頭部姿勢、臉部汗毛和眼鏡。 如需詳細資訊，請參閱 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測)。

## <a name="face-recognition"></a>臉部辨識

臉部辨識廣泛用於許多案例，包括安全性、自然使用者介面、影像內容分析和管理、行動應用程式及機器人。 提供四項臉部識功能：臉部驗證、尋找相似的臉部、臉部分組和人物識別。

### <a name="face-verification"></a>臉部驗證

臉部 API 驗證會對兩個偵測到的臉部執行驗證，或從一個偵測到的臉部向一個人員物件執行驗證。 如需詳細資訊，請參閱 [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (臉部 - 驗證)。

### <a name="finding-similar-face"></a>尋找相似的臉部

指定所偵測的目標臉部和一組要搜尋的候選臉部時，服務會尋找看起來與目標臉部最相似的一小組臉部。 支援兩種工作模式 `matchFace` 和 `matchPerson`。 `matchPerson` 模式會在套用衍生自 [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (臉部 - 驗證) 的相同人員閾值之後，傳回相似的臉部。 `matchFace` 模式會忽略相同人員閾值，並傳回最相似的候選臉部。 下列範例會列出候選臉部。
![概觀 - 尋找相似的臉部](./Images/FaceFindSimilar.Candidates.jpg) 查詢臉部是 ![概觀 - 尋找相似的臉部](./Images/FaceFindSimilar.QueryFace.jpg)

若要尋找四個相似的臉部，`matchPerson` 模式會傳回 (a) 和 (b)，這屬於具有查詢臉部的相同人員。 `matchFace` 模式會傳回 (a)、(b)、(c) 和 (d)，剛好四個候選項目，不過相似度較低。 如需詳細資訊，請參閱 [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (臉部 - 尋找相似)。

### <a name="face-grouping"></a>臉部分組

指定一組未知臉部時，臉部分組 API 會根據相似度自動將其分成數個群組。 每個群組都是原始未知臉部集的適當脫離子集，並包含相似的臉部。 此外，相同群組中的所有臉部可視為屬於相同的人員物件。 如需詳細資訊，請參閱 [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (臉部 - 分組)。

### <a name="face-identification"></a>臉部識別

您可以使用臉部 API，根據偵測到的臉部和人員資料庫 (定義為 LargePersonGroup/PersonGroup) 來識別人員。 請事先建立此資料庫，該資料庫會隨時間進行編輯。

下圖是名為"myfriends" 的 LargePersonGroup/PersonGroup 範例。 每個群組最多可能包含 1,000,000/10,000 個人員物件。 同時，每個人員物件最多可以註冊 248 張臉。

![概觀 - LargePersonGroup/PersonGroup](./Images/person.group.clare.jpg)

建立並定型 LargePersonGroup/PersonGroup 之後，即可對群組和新偵測到的臉部執行識別。 如果臉部識別為影像中的人員物件，則會傳回人員物件。

如需人員識別的詳細資訊，請參閱下列 API 指南：

[Face - Identify (臉部 - 識別)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)
[PersonGroup - Create (PersonGroup - 建立)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
[PersonGroup Person - Create (PersonGroup Person - 建立)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
[PersonGroup - Train (PersonGroup - 定型)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)
[LargePersonGroup - Create (LargePersonGroup - 建立)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)
[LargePersonGroup Person - Create (LargePersonGroup Person - 建立)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)
[LargePersonGroup - Train (LargePersonGroup - 定型)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4)

### <a name="face-storage"></a>臉部儲存體

臉部儲存體可讓標準訂用帳戶在使用 LargePersonGroup/PersonGroup 人員物件 ([PersonGroup Person - Add Face (PersonGroup Person - 新增臉部)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)/[LargePersonGroup Person - Add Face (LargePersonGroup Person - 新增臉部)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)) 或 LargeFaceLists/FaceLists ([FaceList - Add Face (FaceList - 新增臉部)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)/[LargeFaceList - Add Face (LargeFaceList - 新增臉部)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)) 時儲存其他保存的臉部，以透過臉部 API 進行識別或相似度比對。 儲存影像的計費方式為每 1000 張人臉 $0.5，並會依使用天數按比例計算。 免費層訂用帳戶完全免費，但總人數僅限 1,000 名。

臉部儲存體會依天數比例定價。 例如，若您的帳戶在前半個月持續每天使用 10,000 張人臉，後半個月完全未使用，您當月只需支付 10,000 張人臉的儲存天數。 或者，若您於該月期間每天保存 1,000 張人臉數小時並於每晚刪除，每天仍需支付所保存 1,000 張人臉的費用。

## <a name="getting-started-tutorials"></a>使用者入門教學課程

下列教學課程示範臉部 API 的基本功能和訂閱程序：

- [在 CSharp 中開始使用臉部 API 教學課程](Tutorials/FaceAPIinCSharpTutorial.md)
- [在適用於 Android 的 Java 中開始使用臉部 API 教學課程](Tutorials/FaceAPIinJavaForAndroidTutorial.md)
- [在 Python 中開始使用臉部 API 教學課程](Tutorials/FaceAPIinPythonTutorial.md)

## <a name="sample-apps"></a>範例應用程式

看看這些使用臉部 API 的範例應用程式。

- [Microsoft 臉部 API：Windows 用戶端程式庫和範例](https://github.com/Microsoft/Cognitive-Face-Windows)
  - WPF 範例應用程式，示範臉部偵測、分析和識別的數個案例。
- [FamilyNotes UWP app](https://github.com/Microsoft/Windows-appsample-familynotes)
  - 通用 Windows 平台 (UWP) 範例應用程式，透過家庭筆記分享案例來示範語音、Cortana、筆跡和相機的使用。
- [視訊畫面分析範例](https://github.com/microsoft/cognitive-samples-videoframeanalysis)
  - 通用 Windows 平台 (UWP) 範例應用程式，示範如何使用臉部、電腦視覺和表情 API 近乎即時地分析視訊資料流。

## <a name="related-topics"></a>相關主題

- [臉部 API 1.0 版的版本資訊](ReleaseNotes.md)
- [如何偵測影像中的人臉](Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
- [如何識別影像中的人臉](Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
