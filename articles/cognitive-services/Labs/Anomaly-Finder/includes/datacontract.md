---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: e37d3ef5b6f65ad31bc19f9f8c15350014d1c9ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369158"
---
使用[異常搜尋 API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder)，您可以將 JSON 格式的時間序列資料上傳至 API 端點，然後讀取 API 回應的結果。 您可以上傳時間序列資料，每個資料點包括：  
* Timestamp - 資料點的時間戳記。 確定它使用 UTC 日期時間字串，例如 "2017-08-01T00:00:00Z"
* Value - 該資料點的量值

結果包括：
* Period - API 用來偵測異常點的週期性
* WarningText - 可能出現的警告資訊
* ExpectedValue - 學習型模型的預測值
* IsAnomaly - 資料點是否比預測值要高或低，為異常的結果 (尖峰或下降)
* IsAnomaly_Neg - 資料點低於預測值 (下降)
* IsAnomaly_Pos - 資料點高於預測值 (尖峰)
* UpperMargin - ExpectedValue 和 UpperMargin 的總和決定資料點仍屬於正常範圍的上限
* LowerMargin - (ExpectedValue - LowerMargin) 決定資料點仍屬於正常範圍的下限

您可以在[這裡](../apiref.md)找到資料合約的詳細資料。

