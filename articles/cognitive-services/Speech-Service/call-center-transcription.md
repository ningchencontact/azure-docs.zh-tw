---
title: 話務中心轉譯 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音轉換文字的常見案例是轉譯可能來自各種系統 (例如互動式語音回應 (IVR)) 的大量電話語音資料。 音訊可以是立體聲或單聲道，而且是幾乎沒有對訊號執行後處理的原始狀態。 企業可以使用語音服務與整合語音模型，透過許多音訊擷取系統來取得高品質的轉譯。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 37d68a4d2b7658542ebcfdb5d22a10676a8e4d52
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603313"
---
# <a name="speech-services-for-telephony-data"></a>電話語音資料的語音服務

透過有線電話、行動電話和無線電所產生的電話語音資料通常為低品質且在 8 KHz 範圍內的窄頻，其會在語音轉換文字時建立查問。 Azure 語音服務中最新的語音辨識模型擅於轉譯此電話語音資料，即使在人們難以理解資料的情況下亦然。 這些模型是以大量電話語音資料進行定型，具有市面上最佳的辨識精確度，即使在吵雜的環境中亦然。

語音轉換文字的常見案例是轉譯可能來自各種系統 (例如互動式語音回應 (IVR)) 的大量電話語音資料。 這些系統提供的音訊可以是立體聲或單聲道，而且是幾乎沒有對訊號進行後處理的原始狀態。 企業可以使用語音服務與整合語音模型，透過許多音訊擷取系統來取得高品質的轉譯。

電話語音資料可用來進一步了解您客戶的需求、找出新的行銷商機，或評估話務中心服務	專員的績效。 在資料轉譯之後，企業可以使用輸出來改善遙測、識別關鍵片語，或分析客戶情感。

此頁面中概述的技術都是由 Microsoft 在內部針對各種支援通話處理服務 (即時和批次模式) 提供。

讓我們檢閱 Azure 語音服務所提供的一些技術和相關功能。

> [!IMPORTANT]
> 語音服務整合模型是以各種不同的資料進行定型，並且對從聽寫到電話語音分析等案例提供單一模型解決方案。

## <a name="azure-technology-for-call-centers"></a>適用於話務中心的 Azure 技術

在語音服務的功能層面以外，其主要目的 (若套用到話務中心) 是要改善客戶體驗。 這方面有三個清楚的領域存在：

* 通話後分析，也就是通話錄音的批次處理
* 音訊訊號的即時分析處理，可在通話進行時擷取各種見解 (以情感作為顯著的使用案例)，以及
* 虛擬助理 (Bot)，推動客戶與 bot 之間的對話，以嘗試在沒有服務專員參與的情況下解決客戶問題，或作為 AI 通訊協定的應用程式來協助服務專員。

![話務中心轉譯架構](media/scenarios/call-center-transcription-architecture.png)底下的圖片描繪批次案例實作的典型架構圖。

## <a name="speech-analytics-technology-components"></a>語音分析技術元件

不論是通話後或即時領域，Azure 都會提供一組成熟且新興的技術來改善客戶體驗。

### <a name="speech-to-text-stt"></a>語音轉換文字 (STT)

[語音轉換文字](speech-to-text.md)是任何話務中心解決方案中最受歡迎的功能。 因為許多下游分析程序都依賴轉譯的文字，所以字組錯誤率 (WER) 非常重要。 話務中心轉譯的重要挑戰之一就是話務中心常見的雜訊 (例如，其他服務專員在背景中說話)、各種豐富的語言地區設定和方言，以及低品質的實際電話訊號。 WER 與針對特定地區設定定型原音和語言模型的程度高度相互關聯，因此能夠自訂模型以符合您的地區設定很重要。 我們最新的整合 4.x 版模型是轉譯精確度和延遲的解決方案。 使用成千上萬小時的原音資料和大量語彙資訊進行定型，整合模型是市場中轉譯話務中心資料的最精確模型。

### <a name="sentiment"></a>情感
運用於話務中心空間時，測量客戶是否有良好的體驗是語音分析的最重要領域之一。 我們的[批次轉譯 API](batch-transcription.md)會提供每個語句的情感分析。 您可以彙總在通話文字記錄中取得的一組值，以判斷您的服務專員和客戶的通話情感。

### <a name="silence-non-talk"></a>無聲 (未交談)
35% 的支援通話通常會是我們所謂的未交談時間。 發生未交談的案例如下：服務專員正在查閱客戶先前的案例記錄、服務專員正在使用工具，使其存取客戶的桌面並執行功能、客戶正在等候轉接等等。 能測量通話中何時會發生無聲情形極為重要，因為在這幾種案例中會出現數種重要客戶敏感性，而且都發生在通話中。

### <a name="translation"></a>轉譯
有些公司正在試驗提供外國語言支援通話的翻譯文字記錄，以便交付經理了解其客戶的全球體驗。 我們可提供卓越的[翻譯](translation.md)功能。 我們可以從大量的地區設定進行音訊到音訊或音訊到文字的翻譯。

### <a name="text-to-speech"></a>文字轉語音
實作可與客戶互動的 Bot 時，[文字轉換語音](text-to-speech.md)是另一個重要領域。 典型的路徑是客戶說話、其語音會轉譯為文字、分析文字的意圖、根據所辨識的意圖來合成回應，然後將資產呈現給客戶或產生合成的語音回應。 當然，這一切都快速發生 – 因此延遲是這些系統的重要成功元件。

若考量到各種相關技術，例如[語音轉換文字](speech-to-text.md)[LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)、[Bot Framework](https://dev.botframework.com/)、[文字轉換語音](text-to-speech.md)，我們的端對端延遲相當低。

我們的新語音也難以與人聲辨別。 您可以使用我們的語音，賦予您的 Bot 獨特的性格。

### <a name="search"></a>搜尋
分析的另一個主要部分是要識別發生特定事件或體驗時的互動。 通常會透過以下兩種方法完成：隨選搜尋或更具結構化的查詢，在前者中，使用者只要輸入一個片語，系統就會回應，而在後者中，析師可以建立一組邏輯陳述式來識別通話中的案例，然後根據這組查詢來編製每次通話的索引。 以下普遍存在的合規性陳述是一個很好的搜尋範例：「為了確保品質，本次通話會進行錄音... 」– 因為很多公司都想確保其服務專員在通話實際錄音前，會將此免責聲明提供給客戶。 大多數的分析系統都能夠分析查詢/搜尋演算法所找到的行為趨勢 – 因為此趨勢報告終究是分析系統的最重要功能之一。 透過[認知服務目錄](https://azure.microsoft.com/services/cognitive-services/directory/search/)，可以利用編製索引和搜尋功能大幅增強端對端解決方案。

### <a name="key-phrase-extraction"></a>關鍵片語擷取
這是更具挑戰性的分析應用領域之一，而且受惠於 AI 和 ML 的應用。 以下的主要案例是要推斷客戶意圖。 客戶為何來電？ 客戶有何問題？ 客戶為何有負面的體驗？ 我們的[文字分析服務](https://azure.microsoft.com/services/cognitive-services/text-analytics/)提供一組立即可用的分析，可快速升級您的端對端解決方案以擷取這些重要關鍵字或片語。

我們現在可以更仔細地看看語音辨識的批次處理和即時管線。

## <a name="batch-transcription-of-call-center-data"></a>話務中心資料的批次轉譯

為了轉譯大量音訊，我們開發了[批次轉譯 API](batch-transcription.md)。 批次轉譯 API 的開發用意是要以非同步方式轉譯大量的音訊資料。 關於話務中心資料的轉譯，我們的解決方案是以下列要素為基礎：

* **精確度**：透過第四代整合模型，我們可提供卓越的轉譯品質。
* **延遲**：我們了解在進行大量轉譯時，需要快速轉譯。 透過[批次轉譯 API](batch-transcription.md) 起始的轉譯作業會立即排入佇列，而一旦作業開始執行，其執行速度比即時轉譯還要快。
* **安全性**：我們了解通話內容可能包含敏感性資料。 請放心，安全性是我們的最高優先順序之一。 我們的服務已取得 ISO、SOC、HIPAA、PCI 認證。

話務中心會每天產生大量的音訊資料。 如果您的企業將電話語音資料儲存在集中位置 (例如 Azure 儲存體)，您可使用[批次轉譯 API](batch-transcription.md)以非同步方式要求及接收轉譯。

典型的解決方案會使用下列服務：

* Azure 語音服務用於將語音轉譯為文字。 需有語音服務的標準訂用帳戶 (SO)，才能使用批次轉譯 API。 免費訂用帳戶 (F0) 將無法運作。
* [Azure 儲存體](https://azure.microsoft.com/services/storage/)用來儲存電話語音資料，以及批次轉譯 API 所傳回的文字記錄。 此儲存體帳戶應該使用通知，特別是在有新檔案新增的時候。 這些通知用來觸發轉譯程序。
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 用來建立每次錄音的共用存取簽章 (SAS) URI，並觸發 HTTP POST 要求以開始轉譯。 此外，Azure Functions 用來建立使用批次轉譯 API 擷取和刪除轉譯的要求。
* [Webhook](webhooks.md) 用於在轉譯完成時收到通知。

我們在內部使用上述技術來支援批次模式的 Microsoft 客戶通話。
![批次架構](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>話務中心資料的即時轉譯

有些企業需要即時轉譯交談。 即時轉譯可用來識別關鍵字組及觸發交談相關內容和資源的搜尋，以便監視情感，進而改善可存取性，或為不是母語人士的客戶和服務專員提供翻譯。

對於需要即時轉譯的案例，我們建議使用[語音 SDK](speech-sdk.md)。 語音轉換文字目前以[超過 20 種語言](language-support.md)提供，而 SDK 則以 C++、C#、Java、Python、Node.js、Objective-C 和 JavaScript 提供。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) 上取得每種語言的範例。 如需最新消息和更新，請參閱[版本資訊](releasenotes.md)。

我們在內部使用上述技術來即時分析 Microsoft 客戶通話。

![批次架構](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>IVR 上的字組

使用[語音 SDK](speech-sdk.md) 或 [REST API](rest-apis.md)，即可將語音服務輕鬆地整合在任何解決方案中。 不過，話務中心轉譯可能需要額外的技術。 通常，IVR 系統與 Azure 之間需要連線。 雖然我們不提供這類元件，但我們想要描述 IVR 連線的需求。

有數個 IVR 或電話語音服務產品 (例如 Genesys 或 AudioCodes) 提供整合功能，可用來啟用對 Azure 服務的輸入和輸出音訊傳遞。 基本上，自訂 Azure 服務可能提供一個特定介面，用來定義電話通話工作階段 (例如通話開始或通話結束)，並公開一個 WebSocket API，以接收搭配語音服務使用的輸入串流音訊。 輸出回應 (例如交談轉譯或與 Bot Framework 的連線) 可與 Microsoft 的文字轉換語音服務合成並傳回給 IVR 進行播放。

另一個案例是直接 SIP 整合。 Azure 服務連線到 SIP 伺服器，從而取得輸入資料流和輸出資料流，其用於語音轉換文字和文字轉換語音階段。 為了連線到 SIP 伺服器，因而有一些商業軟體供應項目，例如 Ozeki SDK，或 [Teams 通話與會議 API](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (目前為搶鮮版 (Beta))，其設計訴求是要支援這種音訊通話案例。

## <a name="customize-existing-experiences"></a>自訂現有的體驗

Azure 語音服務可順利地與內建模型搭配使用，不過，您可以進一步自訂及調整體驗，以搭配您的產品或環境。 從原音模型調整到專屬於自身品牌的獨特聲音音調，都是自訂選項的範圍。 建立自訂模型之後，您可以即時或以批次模式，將其與任何 Azure 語音服務搭配使用。

| 語音服務 | 模型 | 說明 |
|----------------|-------|-------------|
| 語音轉文字 | [原音模型](how-to-customize-acoustic-models.md) | 針對用於特定環境 (例如汽車或工廠) 的應用程式、工具或裝置建立自訂原音模型，而這每一個的錄音條件都較特殊。 例如，帶有口音的語音、特定背景雜音或使用特定麥克風來錄音。 |
| | [語言模型](how-to-customize-language-model.md) | 建立自訂語言模型來提升特定產業的詞彙和文法轉譯，例如醫療術語或 IT 專業術語。 |
| | [發音模型](how-to-customize-pronunciation.md) | 使用自訂發音模型，您可以定義語音形式和顯示字組或字詞。 它可用於處理自訂的字詞，如產品名稱或縮略字。 您只需要有發音檔 - 簡單的 .txt 檔。 |
| 文字轉換語音 | [聲音音調](how-to-customize-voice-font.md) | 自訂聲音音調可讓您為自己的品牌建立可辨識的獨特聲音。 只需少量資料即可開始建立。 提供的資料愈多，您的聲音音調聽起來就愈自然且愈像真人。 |

## <a name="sample-code"></a>範例程式碼

每個 Azure 語音服務的範例程式碼皆可在 GitHub 上取得。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。 使用下列連結來檢視 SDK 和 REST 範例：

* [語音轉文字及語音翻譯範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音轉文字](rest-speech-to-text.md)
* [REST API：文字轉語音](rest-text-to-speech.md)
* [REST API：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [免費取得語音服務的訂用帳戶金鑰](get-started.md)
