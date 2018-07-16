---
title: Azure 內容仲裁概觀 | Microsoft Docs
description: 了解如何使用內容仲裁，追蹤、標幟、評估及篩選使用者產生之內容中的不當內容。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/15/2017
ms.author: sajagtap
ms.openlocfilehash: cd9e2e9c10e9dc5ba118c8319f76174bf6f0da9f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370350"
---
# <a name="what-is-content-moderator"></a>什麼是內容仲裁？

內容仲裁是監視有無可能的冒犯、不想要及具風險之內容的程序。 仲裁的內容可以是影像、文字和影片。

## <a name="where-it-is-used"></a>使用位置

下列清單顯示一些使用內容仲裁的範例案例：

- 仲裁產品類別目錄和使用者產生之內容的線上市集
- 仲裁使用者產生之遊戲成品和聊天室的遊戲公司
- 仲裁由其使用者新增之影像、文字和影片的社交傳訊平台
- 針對其內容實作集中式內容仲裁的企業媒體公司
- 為學生和授課者篩選錯誤和冒犯內容的 K-12 教育解決方案提供者

## <a name="what-it-includes"></a>包含內容

內容仲裁包含數個 Web 服務 API 和內建人機互動審核工具，可協助仲裁影像、文字和影片。

![內容仲裁區塊圖](images/content-moderator-block-diagram.png)

## <a name="apis"></a>API

內容仲裁包括下列 API：
  - [**文字仲裁 API**](text-moderation-api.md)：使用此 API 掃描文字中有無可能的粗話、偏激、具暗示性、冒犯及個人識別資訊 (PII)。
  - [**自訂字組清單 API**](try-terms-list-api.md)：使用此 API 比對除內建字詞以外的自訂字詞清單。 使用這些清單根據您的內容原則來封鎖或允許內容。  
  - [**影像仲裁 API**](image-moderation-api.md)：使用此 API 掃描影像中有無成人和猥褻內容、使用光學字元辨識 (OCR) 功能偵測影像中的文字，以及偵測臉部。
  - [**自訂影像清單 API**](try-image-list-api.md)：使用此 API 比對自訂影像清單，這是您不需要重新分類的預先識別內容。
  - [**影片仲裁 API**](video-moderation-api.md)：使用此 API 掃描影片中有無潛在的成人和猥褻內容。
  - [**審核 API**](try-review-api-job.md)：使用[工作](try-review-api-job.md)、[審核](try-review-api-review.md)與[工作流程](try-review-api-workflow.md)作業來建立及自動化審核工具中的人機互動工作流程。

## <a name="human-review-tool"></a>人工審核工具

您的內容仲裁訂用帳戶包含內建[人工審核工具](Review-Tool-User-Guide/human-in-the-loop.md)。 使用前述的審核 API 建立文字、影像和影片的審核，以便您的仲裁人員制定最終決策。

![內容仲裁的影片審核工具](images/video-review-default-view.png)

## <a name="next-steps"></a>後續步驟

使用[快速入門](quick-start.md)以開始使用內容仲裁。
