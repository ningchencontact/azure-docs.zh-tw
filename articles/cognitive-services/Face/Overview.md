---
title: 什麼是臉部 API？
titleSuffix: Azure Cognitive Services
description: 了解如何使用臉部服務來偵測及分析影像中的人臉。
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: overview
ms.date: 10/29/2018
ms.author: sbowles
ms.openlocfilehash: a15b6678b15bf5d1a3078494e12da3a08c57bed3
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633456"
---
# <a name="what-is-the-azure-face-api"></a>什麼是 Azure 臉部 API？

Azure 臉部 API 是一項認知服務，可提供演算法來偵測、辨識和分析影像中的人臉。 處理人臉資訊的能力在許多不同的軟體案例中都有其重要性，例如安全性、自然使用者介面、影像內容分析和管理、行動應用程式及機器人。

臉部 API 提供了幾個不同的功能，下列各節會有相關概述。 請繼續閱讀以深入了解每一項功能，並判斷其是否符合您的需求。

## <a name="face-detection"></a>臉部偵測

臉部 API 可以偵測影像中的人臉，並傳回其位置的矩形座標。 臉部偵測也可以擷取一連串與臉部相關的屬性，例如姿勢、性別、年齡、頭部姿勢、臉部汗毛和眼鏡。

![女性與男性影像，其臉部周圍繪有矩形，並顯示其年齡和性別](./Images/Face.detection.jpg)

臉部偵測功能也可透過[電腦視覺 API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) 來取得，但如果您想要對臉部資料執行更深入的作業，請使用臉部 API (本服務)。 如需臉部偵測的詳細資訊，請參閱[偵測 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

## <a name="face-verification"></a>臉部驗證

驗證 API 會對兩個偵測到的臉部執行驗證，或從一個偵測到的臉部向一個人員物件執行驗證。 實際上，它會評估兩張臉孔是否屬於同一人。 這在安全性案例中可能會很有用。 如需詳細資訊，請參閱[驗證 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)。

## <a name="find-similar-faces"></a>尋找類似臉部

「尋找類似項目 API」會取得目標臉部和一組候選臉部，然後尋找看起來與目標臉部最相似的一小組臉部。 目前支援 **matchPerson** 和 **matchFace** 兩種工作模式。 **matchPerson** 模式會在篩選出相同人員後 (使用[驗證 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a))，傳回類似的臉部。 **matchFace** 模式則會忽略相同人員篩選，並傳回不一定屬於同一人的類似候選臉部清單。

在下列範例中，這是目標臉部：

![面帶笑容的女性](./Images/FaceFindSimilar.QueryFace.jpg)

而這些是候選臉部：

![五個面帶笑容人員的影像。 影像 a) 和 b) 是同一人](./Images/FaceFindSimilar.Candidates.jpg)

在尋找四個相似的臉部時，**matchPerson** 模式會傳回 (a) 和 (b)，因為它們描繪的是與目標臉部相同的人員。 **matchFace** 模式會傳回 (a)、(b)、(c) 和 (d) 四個候選項目，不過某些項目不是同一人，或是相似度較低。 如需詳細資訊，請參閱[尋找類似項目 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)。

## <a name="face-grouping"></a>臉部分組

群組 API 會根據相似度將一組陌生臉部分成數個群組。 每個群組都是與原始臉部集合不相連的適當子集。 群組中的臉部可能全都屬於同一人，但同一人可能會有數個不同的群組 (根據其他因素來區分，例如運算式)。 如需詳細資訊，請參閱[群組 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)。

## <a name="person-identification"></a>人員識別

識別 API 可用來識別針對人員資料庫所偵測出的臉部。 對於相片管理軟體中的自動影像標記功能來說，這可能很實用。 您要先建立資料庫，然後可在一段時間過後加以編輯。

下圖說明資料庫 "myfriends" 的範例。 每個群組最多可包含 1,000,000 個不同的人物，且每個人物最多可以註冊 248 個臉部。

![有 3 行的網格，每行有 3 列臉部影像](./Images/person.group.clare.jpg)

資料庫建立並訓練完成後，即可對具有新偵測到臉部的群組執行識別作業。 如果臉部識別為群組中的人員，則會傳回人員物件。

如需人員識別的詳細資訊，請參閱[識別 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)。

## <a name="use-containers"></a>使用容器

[使用臉部容器](face-how-to-install-containers.md)，藉由在更接近資料的位置安裝標準化的 Docker 容器，來偵測、辨識和找出人臉。

## <a name="sample-apps"></a>範例應用程式

下列應用程式範例會說明一些「臉部 API」的使用方式。

- [Microsoft 臉部 API：Windows 用戶端程式庫和範例](https://github.com/Microsoft/Cognitive-Face-Windows) - WPF 應用程式，會示範數個關於臉部偵測、分析和識別的案例。
- [FamilyNotes UWP 應用程式](https://github.com/Microsoft/Windows-appsample-familynotes) - 通用 Windows 平台 (UWP) 應用程式，會在家庭記事分享案例中使用臉部識別以及語音、Cortana、筆跡和相機。

## <a name="next-steps"></a>後續步驟

請遵循快速入門來實作程式碼中的簡單臉部偵測案例。
- [快速入門：搭配使用 .NET SDK 與 C# 來偵測影像中的人臉](quickstarts/csharp.md) (亦有其他可用語言)
