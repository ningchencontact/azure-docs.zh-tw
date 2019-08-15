---
title: 信賴分數 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 信賴分數表示解答對給定的使用者查詢而言的切合程度。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: afc50a5adb591550f6e988a572d1ac9a8c4439cb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955191"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>QnA Maker 知識庫的信賴分數
當使用者查詢與某個知識庫相符時，QnA Maker 會傳回相關的答案以及信賴分數。 此分數表示該答案針對指定之使用者查詢正確比對的信賴度。 

信賴分數是介於 0 到 100 之間的數字。 100 分是極可能完全相符，而 0 分表示找不到相符的答案。 分數越高，表示答案的信賴度越好。 針對指定的查詢，可能會傳回多個答案。 在這種情況下，會以信賴分數遞減的順序傳回答案。

在下列範例中，您可以看到一個 QnA 實體和 2 個問題。 


![範例 QnA 組](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

針對上述的範例，對不同的使用者查詢類型來說，您可以預期分數惠如下列的範例分數範圍：


![順位排定程式分數範圍](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


下表指出與指定分數相關聯的一般信賴度。

|分數值|分數意義|範例查詢|
|--|--|--|
|90 - 100|使用者查詢和 KB 問題接近完全相符|「我的變更在發行後並未在 KB 中更新」|
|> 70|高信賴度：通常是良好的答案，能完整回答使用者查詢|「我發行了我的 KB 但它不會更新」|
|50 - 70|中信賴度：通常是相當不錯的答案，能回答使用者查詢的主要意圖|「我應該要先儲存更新再發行 KB 嗎？」|
|30 - 50|低信賴度：通常是相關聯的答案，能對使用者意圖進行部分的回答|「儲存和訓練的作用為何？」|
|< 30|信賴度非常低：通常無法回答使用者的查詢，但有一些相符的字組或片語 |「我可以從哪裡新增同義字到 KB」|
|0|沒有相符項目，所以不會傳回答案。|「服務的成本是多少」|

## <a name="choose-a-score-threshold"></a>選擇分數閾值
上表顯示大部分 KB 預期的分數。 不過, 由於每個 KB 都不同, 而且有不同類型的單字、意圖和目標, 我們建議您測試並選擇最適合您的閾值。 根據預設, 臨界值會設定為 0, 因此會傳回所有可能的答案。 建議的臨界值適用于大部分的 Kb, 是**50**。

選擇您自己的閾值時，請記住在精確度和涵蓋範圍之間取得平衡，並根據您的需求調整閾值。

- 如果您的案例中**精確度**比較重要，則提升您的閾值。 如此一來，在每次傳回答案時，就會是更高信賴度的情況，且更可能接近使用者所需的答案。 在這種情況下，您最後可能會留下更多未回答的問題。 *例如：* 如果您將閾值設為 **70**，您可能會遺漏一些模稜兩可的問題，例如「什麼是儲存和訓練？」。

- 如果**涵蓋範圍** (或重新叫用) 更重要，且您想要盡可能回答更多問題 (即使答案與使用者的問題只有部分相關)，則您可以降低閾值。 這表示會有更多答案無法回答使用者實際查詢，但能提供一些其他可能相關之答案的情況。 *例如:* 如果您將閾值設為**30**, 則可以提供查詢的答案, 例如「我可以在哪裡編輯 KB？」

> [!NOTE]
> 較新的 QnA Maker 版本包括改善評分邏輯，並可能影響您的閾值。 每當您更新服務時，請務必視需要測試和調整閾值。 您可以[在此](https://www.qnamaker.ai/UserSettings)檢查您的 QnA 服務版本，並[在此](../How-To/troubleshooting-runtime.md)了解如何取得最新的更新。

## <a name="set-threshold"></a>設定閾值 

將臨界值分數設定為[GENERATEANSWER API JSON 主體](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)的屬性。 這表示您會針對每個 GenerateAnswer 呼叫進行設定。 

從 bot framework 中, 使用[C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c)或[node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs)將分數設定為 options 物件的一部分。

## <a name="improve-confidence-scores"></a>改善信賴分數
若要改善使用者查詢特定回應的信賴分數，您可以將使用者查詢加入至知識庫做為該回應的替代問題。 您也可以使用不區分大小寫的[文字變異形式](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) \(英文\)，來將同義字新增至 KB 中的關鍵字。


## <a name="similar-confidence-scores"></a>類似的信賴分數
多個回應有類似的信賴分數時，表示查詢過於不具體，因此有多個同等可能性的相符答案。 請嘗試進一步調整 QnA，以便每個 QnA 實體的意圖彼此相異。


## <a name="confidence-score-differences"></a>信賴分數差異
在測試和發佈的知識庫版本之間，即使內容相同，不過答案的信賴分數可能會略微變化。 這是因為測試和發佈的知識庫位於不同的 Azure 搜尋服務索引中。 發佈知識庫時，知識庫的問題與答案內容會從測試索引移到 Azure 搜尋服務中的生產索引。 請查看[發佈](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)作業的運作方式。

如果您的知識庫位於不同區域，則每個區域都會使用自己的 Azure 搜尋服務索引。 由於會使用不同的索引，因此，分數將不會完全相同。 


## <a name="no-match-found"></a>未找到相符項目
順位排定程式找不到適當的相符項目時，會傳回 0.0 的信賴分數或「無」，而且預設回應是「在資料庫中找不到適當的相符項目」。 您可以在呼叫端點的 bot 或應用程式代碼中覆寫此[預設回應](#change-default-answer)。 或者，您也可以在 Azure 中設定覆寫回應，這會變更特定 QnA Maker 服務中部署的所有知識庫預設值。

## <a name="change-default-answer"></a>變更預設答案

1. 移至 [Azure 入口網站](https://portal.azure.com)，並瀏覽到代表您建立的 QnA Maker 服務之用的資源群組。

2. 按一下以開啟 **App Service**。

    ![在 Azure 入口網站中，存取 QnA Maker 的 App Service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. 按一下 [應用程式設定]，並編輯 [DefaultAnswer] 欄位成為所需的預設回應。 按一下 [儲存]。

    ![選取 [應用程式設定]，然後編輯 QnA Maker 的 DefaultAnswer](../media/qnamaker-concepts-confidencescore/change-response.png)

4. 重新啟動 App Service

    ![變更 DefaultAnswer 之後，請重新啟動 QnA Maker Appservice](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [支援的資料來源](./data-sources-supported.md)

