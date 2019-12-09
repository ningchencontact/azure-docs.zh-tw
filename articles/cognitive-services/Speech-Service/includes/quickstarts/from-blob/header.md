---
title: 快速入門：辨識儲存在 Blob 儲存體中的語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828787"
---
在此快速入門中，您將會使用[批次轉譯 REST API](../../../batch-transcription.md) 來辨識儲存在 [SAS Blob](https://aka.ms/ignite2019/speech/placeholder) 中的語音。 在滿足幾個先決條件之後，使用 REST API 來辨識語音只需採取幾個步驟：
> [!div class="checklist"]
> * 將 JSON 要求傳送至語音服務以開始轉譯語音。
> * 檢查轉譯的狀態。
> * 在完成時下載轉譯結果。