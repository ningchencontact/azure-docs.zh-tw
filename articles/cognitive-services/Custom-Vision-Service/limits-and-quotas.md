---
title: 限制和配額 - 自訂視覺服務
titlesuffix: Azure Cognitive Services
description: 了解自訂視覺服務的限制和配額。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: ce06effbce12abb6271e050829d3218f4fbbfbf4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902853"
---
# <a name="limits-and-quotas"></a>限制和配額

自訂視覺服務有三個金鑰層。 F0 和 S0 資源是透過 Azure 入口網站取得。 如需定價和交易定義的詳細資料，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。  F0 專案可升級至 S0 專案。

有限的試用版專案資源會附加至您的自訂視覺登入 (也就是 AAD 帳戶或 MSA 帳戶)。其目的是供服務的短期試用版使用。  在引進 Azure Preview 之前 (2018 年 3 月 1 日)，於免費預覽早期建立的帳戶會保留其之前有限的試用版配額。 

||**有限的試用版**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|專案|2|2|100|
|每個專案的訓練影像數 (分類)|5,000|5,000|50,000|
|每個專案的訓練影像數 (物件偵測)|5,000|5,000|10,000|
|預測數/月|10,000 |10,000|無限|
|標籤數/專案|50|50|250|
|反覆運算次數 |10|10|10|
|每個標籤的最小標記影像數、分類 (建議使用 50+) |5|5|5|
|每個標籤的最小標記影像數、物件偵測 (建議使用 50+)|15|15|15|
|預測影像的儲存時間|30 天|30 天|30 天|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業及儲存 (每秒交易數)|2|2|10|
|[預測](https://go.microsoft.com/fwlink/?linkid=865445)作業但不儲存 (每秒交易數)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (每秒 API 呼叫數)|2|2|10|
|[其他 API 呼叫](https://go.microsoft.com/fwlink/?linkid=865446) (每秒交易數)|10|10|10|
|最大影像大小 (訓練影像上傳) |6MB|6MB|6MB|
|最大影像大小 (預測)|4MB|4MB|4MB|

S0 專案預期會在一段時間後增加「每個專案的訓練影像數」和「標籤數/每個專案」限制。 
