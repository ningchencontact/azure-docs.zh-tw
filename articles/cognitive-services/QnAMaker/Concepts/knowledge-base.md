---
title: 知識庫 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 知識庫是由一組問題/回答 (QnA) 配對以及與每個 QnA 配對相關聯的選用中繼資料所組成。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 02111ac90fe97ddaddbd41ad42410e7e76f1c405
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311084"
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

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [知識庫的開發生命週期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>請參閱

[QnA Maker 概觀](../Overview/overview.md)
