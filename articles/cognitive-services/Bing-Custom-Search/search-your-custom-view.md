---
title: 搜尋自訂檢視 - Bing 自訂搜尋
titlesuffix: Azure Cognitive Services
description: 描述如何搜尋網頁的自訂檢視。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 9c0c4fc21f6f4b23b14c8020ecd7e95c63dedcbf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388557"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>從入口網站呼叫 Bing 自訂搜尋執行個體

設定自訂搜尋體驗之後，您可以在 Bing 自訂搜尋[入口網站](https://customsearch.ai)中測試。 

![Bing 自訂搜尋入口網站的螢幕擷取畫面](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>建立搜尋查詢 

登入 Bing 自訂搜尋[入口網站](https://customsearch.ai)後，選取搜尋執行個體，然後按一下 [生產]  索引標籤。在 [端點]  之下，選取 API 端點 (例如 Web API)。 您的訂用帳戶可決定顯示的端點。

若要建立搜尋查詢，請輸入您端點的參數值。 請注意，入口網站中顯示的參數可能會隨著您所選的端點而改變。 請參閱[自訂搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)如需詳細資訊。 若要變更您的搜尋執行個體所使用的訂用帳戶、 新增適當的訂用帳戶金鑰，並更新適當的市場及/或語言參數。

一些重要參數如下：


|參數  |描述  |
|---------|---------|
|查詢     | 要搜尋的搜尋字詞。 僅適用於網頁、影像、影片與自動建議端點 |
|自訂組態識別碼 | 所選取自訂搜尋執行個體的組態識別碼。 這是唯讀欄位。 |
|市場     | 結果所源自的市場。 僅適用於網頁、影像、影片與裝載 UI 端點。        |
|訂用帳戶金鑰 | 要用來測試的訂用帳戶金鑰。 您可以從下拉式清單中選取金鑰，或手動輸入一個。          |

按一下 [其他參數]  會顯示下列參數：  

|參數  |描述  |
|---------|---------|
|安全搜尋     | 用來篩選成人內容網頁的篩選條件。 僅適用於網頁、影像、影片與裝載 UI 端點。        |
|使用者介面語言    | 用於使用者介面字串的語言。 例如，若您在裝載 UI 中啟用影像與影片， [影像]  與 [影片]  索引標籤會使用指定的語言。        |
|計數     | 回應中傳回的搜尋結果數目。 僅適用於網頁、影像與影片端點。         |
|Offset    | 傳回結果之前要跳過的搜尋結果數目。 僅適用於網頁、影像與影片端點。        |
    
指定所有必要選項之後，請按一下 [呼叫]  在右窗格中檢視 JSON 回應。 如果您選取裝載 UI 端點，可以從底部窗格測試搜尋體驗。

## <a name="change-your-bing-custom-search-subscription"></a>變更您的 Bing 自訂搜尋訂用帳戶

您可以變更與您的 Bing 自訂搜尋執行個體相關聯，而不需要建立新的執行個體的訂用帳戶。 若要有 API 呼叫傳送，並以新的訂用帳戶收取費用，建立新的 Bing 自訂搜尋資源在 Azure 入口網站中。 使用新的訂用帳戶金鑰，在您的 API 要求，以及您的執行個體自訂組態識別碼。

## <a name="next-steps"></a>後續步驟

- [使用 C# 呼叫您的自訂檢視](./call-endpoint-csharp.md)
- [使用 Java 呼叫您的自訂檢視](./call-endpoint-java.md)
- [使用 NodeJs 呼叫您的自訂檢視](./call-endpoint-nodejs.md)
- [使用 Python 呼叫您的自訂檢視](./call-endpoint-python.md)

- [使用 C# SDK 呼叫您的自訂檢視](./sdk-csharp-quick-start.md)
