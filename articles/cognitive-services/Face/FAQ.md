---
title: 常見問題集 - 臉部 API
titlesuffix: Azure Cognitive Services
description: 以下是有關臉部 API 服務最熱門問題的解答。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351322"
---
# <a name="face-api-frequently-asked-questions"></a>臉部 API 常見問題集

> [!TIP]
> 如果您在此常見問題集中找不到問題的解答，請嘗試在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上詢問臉部 API 社群，或連絡 [UserVoice 協助與支援](https://cognitive.uservoice.com/)。

-----
**問題**：有哪些因素可以減少人臉識別 API 的精確度，辨識、 驗證，或尋找相似嗎？

**答**：通常它是相同的情況下，讓人難以識別人包括：
* 阻擋一或兩個眼睛的障礙物
* Harsh 光源 （比方說，嚴重背光）
* 改變髮型或臉毛
* 因年紀而改變
* 極大的臉部表情，（例如，...）

人臉識別 API 通常是在具挑戰性的情況下，如上所示，成功，但可以降低精確度。 若要讓辨識功能梗強固並解決這些挑戰，請使用包含各種角度和光線的相片訓練您的人員。

-----
**問題**：我正在傳入二進位影像資料，但收到「臉部影像無效」錯誤。

**答**：此錯誤表示演算法已剖析的映像的問題。 原因包括：
* 支援的輸入影像格式包括 JPEG、PNG、GIF (第一個畫面)、BMP。
* 影像檔案大小不得大於 4MB
* 可偵測的臉部大小範圍是 36x36 到 4096x4096 像素。 無法偵測此範圍之外的臉部
* 某些臉部可能不會偵測到由於技術挑戰，例如大的臉部角度 （頭部姿勢），大型的阻擋物。 正面和接近正面的臉部能提供最佳結果

-----
