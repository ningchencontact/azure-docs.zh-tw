---
title: 如何選擇辨識模式 | Microsoft Docs
description: 如何選擇最佳的辨識模式。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368370"
---
# <a name="speech-recognition-modes"></a>語音辨識模式

Microsoft 的「語音轉換文字」API 支援多種語音辨識模式。 請為您的應用程式選擇會產生最佳辨識結果的模式。

| Mode | 說明 |
|---|---|
| 互動 | 適用於互動使用者應用程式案例的「命令和控制」辨識。 使用者說出簡短片語以作為應用程式的命令。 |
| 聽寫 | 適用於聽寫案例的連續辨識。 使用者說出顯示為文字的較長句子。 使用者使用較正式的說話風格。 |
| 交談 | 用於將人類交談記錄成文字的連續辨識。 使用者使用較不正式的說話風格，並且可能交替使用較長句子和較短片語。

> [!NOTE]
> 當您使用 [REST API](../GetStarted/GetStartedREST.md) 時，才適用這些模式。 [用戶端程式庫](../GetStarted/GetStartedClientLibraries.md)會使用不同的參數來指定辨識模式。 如需詳細資訊，請參閱您所選擇的用戶端程式庫。

如需詳細資訊，請參閱[辨識模式](../concepts.md#recognition-modes)頁面。
