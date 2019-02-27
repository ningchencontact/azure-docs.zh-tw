---
title: 辨識印刷和手寫文字 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 辨識影像中印刷和手寫文字的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313172"
---
# <a name="recognizing-printed-and-handwritten-text"></a>辨識印刷和手寫的文字

電腦視覺可從表層和背景不同的各種物件影像中偵測並擷取出印刷或手寫的文字，例如收據、海報、名片、信件和白板等。

文字辨識功能與[光學字元辨識 (OCR)](concept-extracting-text-ocr.md) 非常類似，但與 OCR 不同，它以非同步方式執行並使用更新的辨識模型。

> [!NOTE]
> 這項技術目前為預覽狀態，且只適用於英文文字。

## <a name="text-recognition-requirements"></a>文字辨識需求

電腦視覺可在符合下列需求的影像中辨識印刷和手寫文字：

- 必須以 JPEG、PNG 或 BMP 格式呈現的影像
- 影像的檔案大小必須小於 4 MB
- 影像的大小必須介於 50 x 50 與 4200 x 4200 像素之間

## <a name="next-steps"></a>後續步驟

請參閱[辨識文字參考文件](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)，以取得更多資訊。