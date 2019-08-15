---
title: 知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知識庫是由一組問題/回答 (QnA) 配對以及與每個 QnA 配對相關聯的選用中繼資料所組成。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 022b16669791b9b9cce066b3dd17c70b33569cc0
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955242"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>什麼是 QnA Maker 知識庫？

QnA Maker 知識庫是由一組問題/回答 (QnA) 配對以及與每個 QnA 配對相關聯的選用中繼資料所組成。

## <a name="key-knowledge-base-concepts"></a>重要知識庫概念

* **問題** - 問題包含最能代表使用者查詢的文字。 
* **回答** - 回答是當使用者查詢符合相關聯問題時傳回的回應。  
* **中繼資料** - 中繼資料是與 QnA 配對相關聯的標記，而且以索引鍵/值配對表示。 中繼資料標記用來篩選 QnA 配對，並限制執行查詢比對的集合。

單一 QnA (以數值 QnA 識別碼表示) 具有某個問題的多個變體 (替代問題)，這些變體全都對應至單一回答。 此外, 每個這類配對都可以有多個相關聯的元資料欄位: 一個索引鍵和一個值。

![QnA Maker 知識庫](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>知識庫內容格式

當您將豐富的內容內嵌到知識庫時，QnA Maker 會嘗試將內容轉換為 Markdown。 閱讀[這個](https://aka.ms/qnamaker-docs-markdown-support)部落格，以了解大部分聊天用戶端可理解的 Markdown 格式。

中繼資料欄位是由以冒號分隔的索引鍵/值配對 **(Product:Shredder)** 所組成。 索引鍵和值都必須只包含文字。 中繼資料索引鍵不能包含任何空格。 中繼資料每個索引鍵僅支援一個值。

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>QnA Maker 如何處理使用者查詢以選取最佳答案

定型和[發佈](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base)的 QnA Maker 知識庫會在[GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)接收使用者查詢, 從 bot 或其他用戶端應用程式。 下圖說明接收使用者查詢的過程。

![使用者查詢的排名進程](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

下表將說明此程式:

|步驟|用途|
|--|--|
|1|用戶端應用程式會將使用者查詢傳送至[GENERATEANSWER API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage)。|
|2|Qna Maker 會使用語言偵測、spellers 和斷詞工具, 對使用者查詢進行前置處理。|
|3|這會採取此前置處理來改變使用者查詢, 以取得最佳搜尋結果。|
|4|這項改變的查詢會傳送至 Azure 搜尋服務索引, `top`並接收結果數目。 如果這些結果中沒有正確的答案, 請以`top`稍微增加的值。 在 90% 的查詢中`top` , 通常會有10的值可運作。|
|5|QnA Maker 會套用 advanced 特徵化, 以判斷使用者查詢的提取 Azure 搜尋服務結果是否正確。 |
|6|定型的 ranker 模型會使用步驟5中的功能分數來排名 Azure 搜尋服務的結果。|
|7|新的結果會依排名順序傳回用戶端應用程式。|
|||

使用的功能包括但不限於單字層級的語義、主體中的詞彙層級重要性, 以及深度學習的語義模型, 以判斷兩個文字字串之間的相似性與相關性。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [知識庫的開發生命週期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>另請參閱

[QnA Maker 概觀](../Overview/overview.md)
