---
title: 文字分析概觀 - Azure 認知服務 | Microsoft Docs
description: Azure 認知服務中的「文字分析」包含情感分析、關鍵片語擷取、語言偵測和實體連結。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: a514618713afe2306b6fb99b2f8c038aeac56009
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092198"
---
# <a name="what-is-text-analytics"></a>什麼是文字分析？

「文字分析」服務可為原始非結構化文字，提供進階的自然語言處理。 它包含四個主要功能：情感分析、關鍵片語擷取、語言偵測和實體連結。

## <a name="analyze-sentiment"></a>分析人氣

藉由分析原始文字而獲得有關於正面或負面情感的線索，以[了解](how-tos/text-analytics-how-to-sentiment-analysis.md)客戶對您的品牌或主題有何看法。 此 API 會為每份文件傳回 0 到 1 之間的情感分數，1 代表最正面的情感。<br />
分析模型會使用大量文字主體和 Microsoft 的自然語言技術預先定型。 針對[選取的語言](text-analytics-supported-languages.md)，API 可對您所提供的任何原始文字進行分析及評分。

## <a name="extract-key-phrases"></a>擷取關鍵片語

可自動[擷取關鍵片語](how-tos/text-analytics-how-to-keyword-extraction.md)，以快速識別重點。 例如，假設輸入文字為 "The food was delicious and there were wonderful staff"，文字分析服務即會傳回主要討論要點："food" 和 "wonderful staff"。

## <a name="detect-language"></a>偵測語言種類

[偵測](how-tos/text-analytics-how-to-language-detection.md)輸入文字是以何種語言撰寫的，並按要求針對所提交的每份文件回報單一語言代碼，最多可達 120 種語言。 語言代碼各配有一個分數，表示分數的強度。

## <a name="identify-linked-entities-preview"></a>識別已連結實體 (預覽)

在您的文字中[識別](how-tos/text-analytics-how-to-entity-linking.md)已知的實體，並連結至網路上的其他資訊。 實體連結可辨識並釐清某個字詞何時會作為可個別區分的一個實體、動詞和其他文字形式。

## <a name="typical-workflow"></a>一般工作流程

工作流程很簡單：您提交資料以進行分析，並在程式碼中處理輸出。 分析器會依原狀使用，不需要額外的設定或自訂。

1. [註冊](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)[存取金鑰](how-tos/text-analytics-how-to-access-key.md)。 每次要求時都必須傳入此金鑰。

2. 以 JSON 格式[建立要求](how-tos/text-analytics-how-to-call-api.md#json-schema)，其中會以原始非結構化文字的形式，包含您的資料。

3. 將要求發佈至註冊期間所建立的端點，並附上您想要呼叫的 API：情感分析、關鍵片語擷取、語言偵測或實體識別。

4. 將回應串流處理或儲存至本機。 視要求之不同，結果可能是情感分數、擷取的關鍵片語集合或語言代碼。

輸出會根據識別碼以單一 JSON 文件的形式傳回，且附有您發佈的每個文字文件所產生的結果。 然後您可以分析和視覺化結果，或將結果分類為可付諸行動的見解。

文字分析服務所完成的作業不具狀態。 資料不會儲存在您的帳戶中。

<a name="data-limits"></a>

## <a name="specifications"></a>規格

### <a name="supported-languages"></a>支援的語言

請參閱[文字分析中支援的語言](text-analytics-supported-languages.md)。

### <a name="data-limits"></a>資料限制

所有的文字分析服務端點均接受原始文字資料。 目前的限制是每個文件 5000 個字元；如果您需要分析較大的文件，您可以其分成較小的區塊。 如果您仍需要更高的限制，請[與我們連絡](https://azure.microsoft.com/overview/sales-number/)，以便共同討論您的需求。

| 限制 | 值 |
|------------------------|---------------|
| 單一文件的大小上限 | 依 `String.Length` 測量的 5,000 個字元。 |
| 整體要求的大小上限 | 1 MB |
| 要求中的文件數上限 | 1,000 份文件 |

速率限制為每分鐘呼叫 100 次。 請注意，您可以在單一呼叫中提交大量文件 (最多 1000 份文件)。

### <a name="unicode-encoding"></a>Unicode 編碼

針對文字表示法和字元計數計算，文字分析服務會使用 Unicode 編碼。 您可以使用 UTF-8 和 UTF-16 來提交要求，兩者的字元計數並沒有明顯差異。 如果您使用 `String.Length` 取得字元計數，您所使用的就是我們用來測量資料大小的相同方法。

## <a name="next-steps"></a>後續步驟

首先，試著使用[互動式示範](https://azure.microsoft.com/services/cognitive-services/text-analytics/)。 您可以貼上文字輸入 (最多 5,000 個字元) 以偵測語言 (最多 120 種)、計算情感分數、擷取關鍵片語或識別已連結的實體。 不需要註冊。

當您準備好直接呼叫文字分析服務時：

+ [註冊](how-tos/text-analytics-how-to-signup.md)存取金鑰，並檢閱[呼叫 API](how-tos/text-analytics-how-to-call-api.md) 的步驟。

+ [快速入門](quickstarts/csharp.md)是以 C# 撰寫的 REST API 的呼叫適用的逐步解說。 請了解如何以最少的程式碼提交文字、選擇分析，以及檢視結果。

+ [API 參考文件](//go.microsoft.com/fwlink/?LinkID=759346)提供 REST API 的技術文件。 這些文件支援內嵌呼叫，因此您可以從每個文件頁面中呼叫 API。

+ [外部和社群內容](text-analytics-resource-external-community.md)提供部落格貼文清單，並提供影片示範如何搭配其他工具和技術來使用文字分析。

## <a name="see-also"></a>另請參閱

 [認知服務文件頁面](https://docs.microsoft.com/azure/cognitive-services/)
