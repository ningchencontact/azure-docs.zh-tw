---
title: 知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知識庫是由一組問題/回答 (QnA) 配對以及與每個 QnA 配對相關聯的選用中繼資料所組成。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e40af9b2362ee52a1d00f29cdc112d3c2b9a842
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565858"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>什麼是 QnA Maker 知識庫？

QnA Maker 知識庫是由一組問題/回答 (QnA) 配對以及與每個 QnA 配對相關聯的選用中繼資料所組成。

## <a name="key-knowledge-base-concepts"></a>重要知識庫概念

* **問題** - 問題包含最能代表使用者查詢的文字。 
* **回答** - 回答是當使用者查詢符合相關聯問題時傳回的回應。  
* **中繼資料** - 中繼資料是與 QnA 配對相關聯的標記，而且以索引鍵/值配對表示。 中繼資料標記用來篩選 QnA 配對，並限制執行查詢比對的集合。

單一 QnA (以數值 QnA 識別碼表示) 具有某個問題的多個變體 (替代問題)，這些變體全都對應至單一回答。 此外，每個這類組可以有多個與其相關聯的中繼資料欄位： 一個索引鍵和一個值。

![QnA Maker 知識庫](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>知識庫內容格式

當您將豐富的內容內嵌到知識庫時，QnA Maker 會嘗試將內容轉換為 Markdown。 閱讀[這個](https://aka.ms/qnamaker-docs-markdown-support)部落格，以了解大部分聊天用戶端可理解的 Markdown 格式。

中繼資料欄位是由以冒號分隔的索引鍵/值配對 **(Product:Shredder)** 所組成。 索引鍵和值都必須只包含文字。 中繼資料索引鍵不能包含任何空格。 中繼資料支援每個索引鍵的只有一個值。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何處理使用者查詢，以選取最佳的回應

定型並[發行](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)QnA Maker 知識庫在收到來自 bot 或其他用戶端應用程式中，使用者查詢[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。 收到使用者查詢時下, 圖說明此程序。

![排名程序，以供使用者查詢](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

下表會說明的程序：

|步驟|用途|
|--|--|
|1|用戶端應用程式傳送的使用者查詢[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|Qna Maker 前置處理使用者查詢語言偵測、 spellers，與斷詞工具。|
|3|這個前置處理是擷取改變使用者為了得到最佳搜尋結果的查詢。|
|4|此變更的查詢會傳送至 Azure 搜尋服務索引，接收`top`結果數目。 如果在這些結果中，不正確的答案，增加值`top`稍微。 一般值為 10 的`top`90%的查詢中的運作方式。|
|5|QnA Maker 適用於進階的功能，以判斷使用者查詢擷取的 Azure 搜尋服務結果的正確性。 |
|6|定型的 ranker 模型會使用功能分數，從步驟 5，以 Azure 搜尋服務結果的排名。|
|7|新的結果會依此次序傳回至用戶端應用程式。|
|||

所使用的功能包括，但不限於 word 層級語意、 詞彙層級主體，和深入學習的語意模型，來判斷相似度和兩個文字字串之間的關聯性的重要性。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [知識庫的開發生命週期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>另請參閱

[QnA Maker 概觀](../Overview/overview.md)
