---
title: Azure 認知服務語音案例
description: 案例
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: v-jerkin
ms.openlocfilehash: 98e79112ba70ff578975cea9d872b4fcc41512bc
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2018
ms.locfileid: "42093870"
---
# <a name="speech-scenarios"></a>語音案例

有許多案例均可使用語音技術強化能力。 我們在文中分析一些最常見並指出相關功能。 對於大部分的內容，[SDK](speech-sdk.md) 是實現這些案例不可或缺的一部分。

本頁討論如何：
> [!div class="checklist"]
> * 建立語音觸發式應用程式
> * 謄寫話務中心音訊通話
> * 語音機器人

## <a name="voice-triggered-apps"></a>語音觸發式應用程式

許多使用者都想要對應用程式啟用語音輸入功能。 想讓應用程式使用有彈性、免持使用 (例如在車中) 或加快不同工作的速度，例如詢問方向、新聞或天氣資訊，語音輸入是最適合的方式。 

### <a name="voice-triggered-apps-with-baseline-models"></a>語音觸發式應用程式搭配基準模型

如果要讓一般大眾在背景雜音不太大的環境中使用您的應用程式，最簡單、快速的方法就是直接下載[語音 SDK](speech-sdk.md) 並遵循下列相關[範例](quickstart-csharp-dotnet-windows.md)。 由 [Azure 訂用帳戶金鑰](https://azure.microsoft.com/try/cognitive-services/)提供支援的 SDK，可讓開發人員將音訊上傳至 Cortana 與 Skype 所採用的基準語音辨識模型。 模型相當先進而且由先前提及的產品採用。 您可以在幾分鐘內啟動並執行。

### <a name="voice-triggered-apps-with-custom-models"></a>語音觸發式應用程式搭配自訂模型

如果您的應用程式可滿足特定領域需求 (假設化學、生物或特殊飲食需求)，您可能要考慮採用[語言模型](how-to-customize-language-model.md)。 採用語言模型可教導解碼器有關應用程式最常用的片語與字詞。 解碼器使用特定領域的自訂語言模型 (而不是基準模型)，將能更準確地謄寫語音輸入。 同樣地，如果您的應用程式將使用之處的背景雜音很明顯，您可能會想要採用原音模型。 探索其他可創造價值的[語言採用](how-to-customize-language-model.md)與[原音採用](how-to-customize-acoustic-models.md)案例文件，以及瀏覽我們的[採用入口網站](https://customspeech.ai)，以開始建立模型。 類似於基準模型，自訂模型是透過我們的[語音 SDK](speech-sdk.md) 呼叫並遵循相關[範例](quickstart-csharp-dotnet-windows.md)。

## <a name="transcribe-call-center-audio-calls"></a>謄寫話務中心音訊通話

話務中心會累積大量音訊。 透過謄寫可取得這些音訊檔案內隱含的價值。 取得通話文字記錄，可探索通話時間長度、意見、客戶滿意度，以及通話提供給來電者的一般價值。

[批次謄寫 API](batch-transcription.md) 與相關[範例](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)是最佳的起點。

您必須先取得 [Azure 訂用帳戶金鑰](https://azure.microsoft.com/try/cognitive-services/)，然後需要參閱[批次謄寫 API](batch-transcription.md) 文件。

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>使用基準模型來謄寫話務中心音訊通話

您需要決定要使用內部基準模型來執行謄寫、採用語言或原音模型，或兩者。 若要使用基準模型，API 只需要 API 金鑰。 就內部而言，API 會為您的資料叫用最適合的模型並採用。

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>使用自訂模型謄寫話務中心音訊通話

如果您打算使用自訂模型，您需要該模型的識別碼與 API 金鑰。 您可以從[採用入口網站](https://customspeech.ai)取得模型識別碼。 這不是您在 [端點詳細資料] 檢視中所找到的端點識別碼，而是您按一下該模型的 [詳細資料] 時可擷取的模型識別碼。

## <a name="voice-bots"></a>語音機器人

開發人員可利用語音輸出強化其應用程式的能力。 語音服務可合成多種[語言](supported-languages.md)的語音，並提供存取和新增應用程式功能的[端點](rest-apis.md)。

此外，對於想要對其機器人增添更多個性和獨特性的使用者，「語音服務」可讓開發人員自訂獨特的聲音音調。 類似於自訂語音辨識模型，聲音音調需要使用者資料。 開發人員會在我們的[語音採用入口網站](https://customspeech.ai)中上傳該資料，並開始為您的機器人建置獨特語音風格。 細節詳述於[這裡](how-to-text-to-speech.md)與[常見問題集](faq-text-to-speech.md)頁面 

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [開始使用語音 SDK](speech-sdk.md)
