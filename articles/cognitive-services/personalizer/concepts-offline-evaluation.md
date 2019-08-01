---
title: 離線評估 - 個人化工具
titleSuffix: Azure Cognitive Services
description: 使用個人化工具服務在此 C# 快速入門中建立意見反應迴圈。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 5e9e745d73623e03e2530e1712a50e6670ee7ed3
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662856"
---
# <a name="offline-evaluation"></a>離線評估

離線評估是可讓您測試及評估個人化工具服務效用的方法，而這不會變更您的程式碼或影響使用者體驗。 離線評估會使用過去的資料 (從您應用程式傳送至排名 API) 來比較不同排名執行的方式。

離線評估會根據日期範圍來執行。 該範圍的最終時間可以是目前的時間。 範圍起點不能超過[資料保留](how-to-settings.md)的指定天數。

離線評估可協助您回答下列問題：

* 個人化工具排名對成功的個人化有何效果？
    * 個人化工具線上機器學習原則能達到的平均獎勵有哪些？
    * 個人化工具如何與應用程式預設執行的功能成效做比較？
    * 隨機個人化選擇的比較成效為何？
    * 針對手動指定的不同學習原則，其比較成效為何？
* 哪些情境特性對成功的個人化有較多或較少貢獻？
* 哪些動作特性對成功的個人化有較多或較少貢獻？

此外，離線評估可用來探索最佳化程度更高的學習原則，未來可讓個人化工具用來改善結果。

離線評估不會針對用於探索的事件百分比提供指導。

## <a name="prerequisites-for-offline-evaluation"></a>離線評估的必要條件

代表性離線評估的重要考量如下：

* 有足夠的資料。 建議的最小值必須至少為 50,000 個事件。
* 從具有代表性使用者行為與流量的週期中收集資料。

## <a name="discovering-the-optimized-learning-policy"></a>探索最佳化的學習原則

個人化工具可使用離線評估程序來自動探索更好的學習原則。

執行離線評估之後，您可以看到新原則與目前線上原則相比之下，個人化工具所具有的比較成效。 接著, 您可以套用該學習原則, 讓它在個人化工具中立即生效, 方法是下載它, 然後在 [模型和原則] 面板中上傳它。 您也可以下載它以供日後分析或使用。

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>了解離線評估結果的相關性

當您執行離線評估時，務必要分析結果的「信賴界限」  。 如果界限很寬，表示您的應用程式未收到足夠的資料，無法準確或有效地預估獎勵。 由於系統會累積更多資料，而且您會對更長的期間執行離線評估，因此信賴區間會變得愈來愈窄。

## <a name="how-offline-evaluations-are-done"></a>離線評估的運作方式

離線評估會使用名為**反事實評估**的方法來進行。 

個人化工具的基礎假設是使用者行為 (和獎勵) 無法回溯預測 (如果對使用者顯示的內容個與他們看到的內容不同，則個人化工具無法得知會發生什麼事)，並且只能從預估的獎勵中了解。 

這是用於評估的概念程序：

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call
    
        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.
        
    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

離線評估只會使用觀察到的使用者行為。 此程序會捨棄大量的資料，特別是如果您的應用程式執行具有大量動作的排名呼叫。


## <a name="evaluation-of-features"></a>特性評估

離線評估可讓您了解有多少特定動作特性或情境特性在針對更高的獎勵而進行權衡。 資訊會使用評估來計算，並以指定期間和資料為依據，而且可能會因為時間不同而有所差異。

我們建議您查看特性評估並詢問：

* 您的應用程式或系統可提供什麼其他更有效果的額外特性？
* 可以移除哪些效果低的特性？ 效果低的特性會加重機器學習中的「干擾」  。
* 是否有任何意外加入的特性？ 範例如下：個人識別資訊 (PII) 或重複識別碼等等。
* 是否有任何不可用的特性，也就是因為法規或責任考量而不應該用來個人化的特性？ 是否有可以取代不可用特性的特性 (也就是即近似或相關的特性)？


## <a name="next-steps"></a>後續步驟

[設定個人化工具](how-to-settings.md)
[執行離線評估](how-to-offline-evaluation.md)瞭解[個人化工具的運作方式](how-personalizer-works.md)
