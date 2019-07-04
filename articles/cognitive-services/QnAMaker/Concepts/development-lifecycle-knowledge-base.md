---
title: 知識庫的生命週期 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 在反覆的週期中學習模型變更、語句範例、發佈資料以及從端點查詢收集資料時，會有最佳學習成效。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6ffc8931f23835f096c99480b286422fc6e20119
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447624"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker 中的知識庫生命週期
QnA Maker 在反覆的週期中學習模型變更、語句範例、發佈資料以及從端點查詢收集資料時，會有最佳學習成效。 

![編寫週期](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>建立 QnA Maker 知識庫
QnA Maker 知識庫 (KB) 端點會根據知識庫的內容對使用者查詢提供最相符的解答。 建立知識庫是指為問題、解答和相關聯的中繼資料設定內容存放庫的一次性動作。 知識庫可藉由對既有的內容編目來建立，這些內容包括常見問題集頁面、產品手冊或結構化問答組。 了解如何[建立知識庫](../How-To/create-knowledge-base.md)。

## <a name="testing-and-updating-the-knowledge-base"></a>測試和更新知識庫

知識庫在填入內容 (透過編輯或自動擷取) 後即可供測試。 互動式可以測試在 QnA Maker 入口網站中透過**測試**面板輸入使用者的常用查詢，並確認具有足夠的信心分數與正確的回應傳回的回應。 

* **若要修正低的信心分數**： 新增替代的問題。 
* **當查詢不正確地傳回[預設回應](confidence-score.md#change-default-answer)** ： 加入新問題的解答。 

「測試-更新」的這個密封迴圈會持續執行，直到您得到滿意的結果為止。 了解如何[測試知識庫](../How-To/test-knowledge-base.md)。

適用於大型的 Kb 以及使用自動化的測試[generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api)並`isTest`body 屬性哪些查詢`test`知識庫，而不是已發行的知識庫。 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>發佈知識庫
完成知識庫的測試後，您即可發佈知識庫。 在發佈時，會將經過測試的最新版知識庫推送至代表**已發佈**知識庫的專用 Azure 搜尋服務索引。 它也會建立可在您的應用程式或聊天機器人中呼叫的端點。

如此，對測試版知識庫所做的任何變更，都不會對可能正在生產應用程式中執行的已發行版本造成影響。

這些知識庫全都可以個別進行測試。 使用 Api，您可以測試的版本為目標知識庫`isTest`主體 generateAnswer 呼叫中的屬性。

了解如何[發佈知識庫](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="monitor-usage"></a>監視使用量
若要能夠記錄服務的聊天記錄，您必須在[建立 QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)時啟用 Application Insights。

您可以取得服務使用情形的各種分析。 深入了解如何使用 Application Insights 取得 [QnA Maker 服務的分析](../How-To/get-analytics-knowledge-base.md)。

您可以根據從分析得出的結論，適當[更新您的知識庫](../How-To/edit-knowledge-base.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [信賴分數](./confidence-score.md)

## <a name="see-also"></a>請參閱 

[知識庫](./knowledge-base.md)
[QnA Maker 概觀](../Overview/overview.md)
