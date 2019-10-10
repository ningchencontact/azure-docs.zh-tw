---
title: 知識庫的生命週期 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 在反覆的週期中學習模型變更、語句範例、發佈資料以及從端點查詢收集資料時，會有最佳學習成效。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 52d4efaba25dcede3322586d05fceb4d166f51e3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973812"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>QnA Maker 中的知識庫生命週期
QnA Maker 在反覆的週期中學習模型變更、語句範例、發佈資料以及從端點查詢收集資料時，會有最佳學習成效。 

![編寫週期](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>建立 QnA Maker 知識庫
QnA Maker 知識庫 (KB) 端點會根據知識庫的內容對使用者查詢提供最相符的解答。 建立知識庫是指為問題、解答和相關聯的中繼資料設定內容存放庫的一次性動作。 知識庫可藉由對既有的內容編目來建立，這些內容包括常見問題集頁面、產品手冊或結構化問答組。 了解如何[建立知識庫](../quickstarts/create-publish-knowledge-base.md)。

## <a name="testing-and-updating-the-knowledge-base"></a>測試和更新知識庫

知識庫在填入內容 (透過編輯或自動擷取) 後即可供測試。 互動式測試可以透過**測試**面板在 QnA Maker 入口網站中完成，方法是輸入一般使用者查詢，並確認傳回的回應具有正確的回應和足夠的信賴分數。 

* **若要修正低信賴分數**：新增替代問題。 
* **當查詢不正確地傳回[預設回應](confidence-score.md#change-default-answer)時**：請將新答案新增至正確的問題。 

「測試-更新」的這個密封迴圈會持續執行，直到您得到滿意的結果為止。 了解如何[測試知識庫](../How-To/test-knowledge-base.md)。

針對大型 Kb，請使用[GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api)的自動化測試，以及查詢 @no__t 2 知識庫（而不是已發行的知識庫）的 `isTest` body 屬性。 

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

這些知識庫全都可以個別進行測試。 您可以使用 Api，以 generateAnswer 呼叫中的 `isTest` body 屬性作為目標的測試版本。

了解如何[發佈知識庫](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="monitor-usage"></a>監視使用量
若要能夠記錄服務的聊天記錄，您必須在[建立 QnA Maker 服務](../How-To/set-up-qnamaker-service-azure.md)時啟用 Application Insights。

您可以取得服務使用情形的各種分析。 深入了解如何使用 Application Insights 取得 [QnA Maker 服務的分析](../How-To/get-analytics-knowledge-base.md)。

您可以根據從分析得出的結論，適當[更新您的知識庫](../How-To/edit-knowledge-base.md)。

## <a name="version-control-of-a-knowledge-base"></a>知識庫的版本控制

QnA Maker 不提供版本控制。 您必須從 [**設定**] 頁面匯出知識庫，並使用您自己的方法和工具。

從 [**設定**] 頁面完成將知識庫匯出為 TSV 或 XLS 格式的作業。 

當您需要回到特定版本時，您需要從本機系統匯入該檔案。 在 [**設定**] 頁面上，匯入 TSV 或 XLS 檔案。 這會將目前知識庫中的問題和答案取代為匯入檔案的內容。   

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [信賴分數](./confidence-score.md)