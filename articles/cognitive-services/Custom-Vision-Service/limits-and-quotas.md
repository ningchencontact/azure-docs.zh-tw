---
title: 限制和配額 - 自訂視覺服務
titlesuffix: Azure Cognitive Services
description: 了解自訂視覺服務的限制和配額。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 85abc4a50710629d3485d05115698e59a93fc96e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472435"
---
# <a name="limits-and-quotas"></a>限制和配額

有兩個層級的自訂視覺服務的索引鍵。 您可以透過 Azure 入口網站註冊 F0 (免費) 或 S0 (標準) 訂用帳戶。 請參閱對應的[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)，以取得有關定價和交易的詳細資料。

S0 專案預期會在一段時間後增加「每個專案的訓練影像數」和「每個專案的標籤數」。

||**F0**|**S0**|
|-----|-----|-----|
|專案|2|100|
|每個專案的訓練影像數 |5,000|100,000|
|預測數/月|10,000 |無限|
|標籤數/專案|50|500|
|反覆運算次數 |10|10|
|標示為每個標記，分類 （50 + 建議使用） 的映像的最小值 |5|5|
|標示為每個標記，物體偵測 （50 + 建議使用） 的映像的最小值|15|15|
|預測影像的儲存時間|30 天|30 天|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業及儲存 (每秒交易數)|2|10|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業但不儲存 (每秒交易數)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (每秒 API 呼叫數)|2|10|
|[其他 API 呼叫](https://go.microsoft.com/fwlink/?linkid=865446) (每秒交易數)|10|10|
|最大影像大小 (訓練影像上傳) |6 MB|6 MB|
|最大影像大小 (預測)|4 MB|4 MB|
