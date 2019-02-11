---
title: 常見問題集 - 表情 API
titlesuffix: Azure Cognitive Services
description: 提供表情 API 常見問題集的解答。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: ded91c6de498b130cc26109a70e89955dd70d862
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208979"
---
# <a name="emotion-api-frequently-asked-questions"></a>表情 API 常見問題集

> [!IMPORTANT]
> 表情 API 將於 2019 年 2 月 15 日淘汰。 表情辨識功能現已公開推出，是[臉部 API](https://docs.microsoft.com/azure/cognitive-services/face/) 的一部分。

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>如果您在此常見問題集中找不到問題的解答，請嘗試在 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) 上詢問表情 API 社群，或連絡 [UserVoice 協助與支援](https://cognitive.uservoice.com/)。  

-----

**問題**：*哪些類型的影像可從表情 API 獲得最佳結果？*

**答**：使用未遮擋、完全正面的臉部影像，可獲得最佳結果。 部分正面人臉部的可靠性會降低，而且在臉部旋轉超過 45 度的影像中，表情 API 可能無法辨識表情。

-----

**問題**：*表情 API 可識別多少表情？*

**答**：表情 API 可辨識八個普遍接受的不同表情：
* 快樂
* 憂傷
* 驚奇
* 憤怒
* 恐懼
* 蔑視
* 討厭
* 中性

-----

**問題**：*是否有任何方法可將即時影音串流傳遞至此 API 並同時取得結果？*

**答**：使用以影像為基礎的表情 API，並且在每個畫面上呼叫它，或略過畫面以提升效能。  有視訊逐畫面分析範例可以使用。

-----

**問題**：*我正在傳入二進位影像資料，但出現：「臉部影像無效」。**

**答**：此訊息表示演算法有剖析影像的問題。  
* 支援的輸入影像格式包括 JPEG、PNG、GIF (第一個畫面)、BMP
* 影像檔案大小不得大於 4MB
* 可偵測的臉部大小範圍為 36 x 36 至 4096 x 4096 像素。 無法偵測此範圍之外的臉部
* 某些臉部可能因為技術挑戰而無法偵測，例如過大的臉部角度 (頭部姿勢)、大型的阻擋物等。 正面和接近正面的臉部能提供最佳結果

-----
