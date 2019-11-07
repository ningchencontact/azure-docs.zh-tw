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
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500772"
---
在此快速入門中，您將會使用[批次轉譯 REST API](../../../batch-transcription.md) 來辨識儲存在 [SAS Blob](https://aka.ms/ignite2019/speech/placeholder) 中的語音。 在滿足幾個先決條件之後，使用 REST API 來辨識語音只需採取幾個步驟：
> [!div class="checklist"]
> * 將 JSON 要求傳送至語音服務以開始轉譯語音。
> * 檢查轉譯的狀態。
> * 在完成時下載轉譯結果。