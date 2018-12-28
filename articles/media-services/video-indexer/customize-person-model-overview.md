---
title: 在影片索引器中自訂人員模型 - Azure
titlesuffix: Azure Media Services
description: 本文概要說明影片索引器中的人員模型是什麼，以及如何加以自訂。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283234"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在影片索引器中自訂人員模型


影片索引器支援視訊內容的臉部偵測和名人辨識。 根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，名人辨識功能涵蓋大約 1,000,000 個人臉。 偵測到名人辨識功能無法辨識的人臉，但是不會透露姓名。 將視訊上傳到影片索引器並取回結果之後，您可以返回，並命名無法辨認的人臉。 一旦您以姓名標示人臉之後，這張臉和姓名就會新增至您帳戶的人員模型中。 之後，影片索引器會未來的視訊和過去的視訊中辨識這張臉。

您可以使用影片索引器網站或 API，編輯您帳戶的視訊中偵測到的人臉，如下列主題中所述：

- [使用 API 自訂人員模型](customize-person-model-with-api.md)
- [使用網站自訂人員模型](customize-person-model-with-website.md)
