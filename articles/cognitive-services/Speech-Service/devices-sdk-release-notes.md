---
title: 語音裝置 SDK 文件
titleSuffix: Azure Cognitive Services
description: 版本資訊 - 最近版本中的變更
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: wellsi
ms.openlocfilehash: 1c91cde45a6a420376af36f70487adf7fe0ee83a
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751802"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>認知服務語音裝置 SDK 的版本資訊
下列各節列出最新版本中的變更。

## <a name="speech-devices-sdk-151"></a>語音裝置 SDK 1.5.1:

*   包含[交談文字記錄](conversation-transcription-service.md)範例應用程式。
*   更新[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 1.5.1 版的元件。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>SDK 1.5.0 認知服務語音裝置：2019 年發行

*   語音裝置 SDK 現在是 GA，而不再受管制的預覽。
*   更新[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 1.5.0 版的元件。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。
*   新的線上醒機 word 技術帶來顯著的品質改良功能，請參閱重大變更。
*   改善的最欄位辨識的新音效處理管線。

**重大變更**

*   因為新的線上醒機 word 技術所有網路喚醒文字時，必須在我們改善網路喚醒 word 入口網站重新建立。 若要完全移除舊的關鍵字，從裝置解除安裝舊版的應用程式。
    - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>認知服務語音裝置 SDK 1.4.0:2019 年 4 月版本 

* 更新[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 1.4.0 版的元件。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>認知服務語音裝置 SDK 1.3.1:2019 年 5 月版本 

* 更新[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 1.3.1 版的元件。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。 
*   更新網路喚醒 word 處理，請參閱重大變更。
*   範例應用程式新增語音辨識和翻譯語言的選擇。

**重大變更** 

*   [安裝 網路喚醒 word](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)已經過簡化，它現在是應用程式的一部分並不需要另外安裝在裝置上的。
*   網路喚醒字詞辨識已變更，且支援兩個事件。
    - RecognizingKeyword，表示語音結果包含文字 （未經驗證） 的關鍵字。
    - RecognizedKeyword，指出辨識給定的關鍵字該關鍵字辨識已完成。


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>認知服務語音裝置 SDK 1.1.0：2018 年 11 月版本 

* 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 1.1.0。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。 
* 遠場語音辨識精確度已藉由增強的音訊處理演算法獲得提升。
* 範例應用程式新增了中文語音辨識支援。

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>認知服務語音裝置 SDK 1.0.1：2018 年 10 月版本 

* 已將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 1.0.1。 如需詳細資訊，請參閱其[版本資訊](https://aka.ms/csspeech/whatsnew)。 
* 語音辨識精確度將藉由我們已改良的音訊處理演算法獲得提升  
* 已修正一個連續辨識音訊工作階段 Bug。

**重大變更** 

* 此版本引進了幾個重大變更。 如需有關 API 的詳細資料，請參閱[此頁面](https://aka.ms/csspeech/breakingchanges_1_0_0) \(英文\)。 
* KWS 模型檔案與「語音裝置 SDK 1.0.1」不相容。 在將新的「喚醒字」檔案寫入裝置之後，便會刪除現有的「喚醒字」檔案。 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>認知服務語音裝置 SDK 0.5.0：2018 年 8 月版本

* 已透過修正音訊處理程式碼中的錯誤，改善語音辨識的精確度。
* 將[語音 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) 元件版本更新至 0.5.0。 如需詳細資訊，請參閱其[版本資訊](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)。

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>認知服務語音裝置 SDK 0.2.12733：2018 年 5 月版本

認知服務語音裝置 SDK 的第一個公開預覽版本。
