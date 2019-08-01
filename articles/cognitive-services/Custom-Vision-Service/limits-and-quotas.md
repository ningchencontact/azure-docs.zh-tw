---
title: 限制和配額 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 了解自訂視覺服務的限制和配額。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 37921c655cc3c5de5c3c5079eda47fb7513fdf9f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560938"
---
# <a name="limits-and-quotas"></a>限制和配額

自訂視覺服務有兩個金鑰層。 您可以透過 Azure 入口網站註冊 F0 (免費) 或 S0 (標準) 訂用帳戶。 請參閱對應的[認知服務定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)，以取得有關定價和交易的詳細資料。

S0 專案預期會在一段時間後增加「每個專案的訓練影像數」和「每個專案的標籤數」。

||**F0**|**S0**|
|-----|-----|-----|
|專案|2|100|
|每個專案的訓練影像數 |5,000|100,000|
|預測數/月|10,000 |無限制|
|標籤數/專案|50|500|
|反覆運算次數 |10|10|
|每個標記的最小標記影像, 分類 (建議為 50 +) |5|5|
|每個標記的最小標記影像、物件偵測 (建議為 50 +)|15|15|
|預測影像的儲存時間|30 天|30 天|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業及儲存 (每秒交易數)|2|10|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業但不儲存 (每秒交易數)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (每秒 API 呼叫數)|2|10|
|[其他 API 呼叫](https://go.microsoft.com/fwlink/?linkid=865446) (每秒交易數)|10|10|
|最大影像大小 (訓練影像上傳) |6 MB|6 MB|
|最大影像大小 (預測)|4 MB|4 MB|
|每個物件偵測訓練影像的最大區域|200|200|
|每個分類影像的標記上限|30|30|
