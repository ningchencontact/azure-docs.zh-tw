---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細說明了語音傘 helm 圖表組態選項。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717240"
---
### <a name="speech-umbrella-chart"></a>語音 （傘狀圖）

最上層 「 保護傘 」 圖表中的值覆寫對應的子圖表值。 因此，所有內部部署自訂的值應該在此新增。

|參數|描述|預設|
| -- | -- | -- | -- |
| `speechToText.enabled` | 是否**語音轉換文字**服務已啟用。 | `true` |
| `speechToText.verification.enabled` | 是否`helm test`功能**語音轉換文字**服務已啟用。 | `true` |
| `speechToText.verification.image.registry` | Docker 映像儲存機制所`helm test`會使用來測試**語音轉換文字**服務。 Helm 會建立用於測試叢集內的個別 pod，並提取*測試使用*此登錄中的映像。 | `docker.io` |
| `speechToText.verification.image.repository` | Docker 映像儲存機制所`helm test`會使用來測試**語音轉換文字**服務。 Helm 測試 pod 會使用此存放庫提取*測試使用*映像。 | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | 搭配使用 docker 映像標記`helm test`for**語音轉換文字**服務。 Helm 測試 pod 會使用這個標籤提取*測試使用*映像。 | `latest` |
| `speechToText.verification.image.pullByHash` | 是否*測試使用*雜湊提取 docker 映像。 如果`true`，`speechToText.verification.image.hash`應該加入，以有效的映像雜湊值。 | `false` |
| `speechToText.verification.image.arguments` | 用來執行的引數*測試使用*docker 映像。 Helm 測試 pod 會傳遞這些引數至容器時執行`helm test`。 | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | 是否**文字轉換語音**服務已啟用。 | `true` |
| `textToSpeech.verification.enabled` | 是否`helm test`功能**語音轉換文字**服務已啟用。 | `true` |
| `textToSpeech.verification.image.registry` | Docker 映像儲存機制所`helm test`會使用來測試**語音轉換文字**服務。 Helm 會建立用於測試叢集內的個別 pod，並提取*測試使用*此登錄中的映像。 | `docker.io` |
| `textToSpeech.verification.image.repository` | Docker 映像儲存機制所`helm test`會使用來測試**語音轉換文字**服務。 Helm 測試 pod 會使用此存放庫提取*測試使用*映像。 | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | 搭配使用 docker 映像標記`helm test`for**語音轉換文字**服務。 Helm 測試 pod 會使用這個標籤提取*測試使用*映像。 | `latest` |
| `textToSpeech.verification.image.pullByHash` | 是否*測試使用*雜湊提取 docker 映像。 如果`true`，`textToSpeech.verification.image.hash`應該加入，以有效的映像雜湊值。 | `false` |
| `textToSpeech.verification.image.arguments` | 若要執行的引數*測試使用*docker 映像。 Helm 測試 pod 會傳遞這些引數至容器時執行`helm test`。 | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |