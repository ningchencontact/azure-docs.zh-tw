---
title: 什麼是個人化工具？
titleSuffix: Azure Cognitive Services
description: 個人化工具是雲端式 API 服務，可讓您選擇最佳體驗來對使用者展現，進而從其即時行為中學習。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 3132d31e9e45718fa95c39a1b8160ea303ded25d
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883656"
---
# <a name="what-is-personalizer"></a>什麼是個人化工具？

Azure 個人化工具是雲端式 API 服務，可讓您選擇最佳體驗來對使用者展現，進而從其即時行為中學習。

* 提供您的使用者和內容相關資訊，並接收排名最高的動作以顯示您的使用者。 
* 使用個人化工具前，不需要清理和標記資料。
* 方便時，將意見反應提供給個人化工具。 
* 檢視即時分析。 
* 在大型資料科學工作中使用個人化工具來驗證現有的實驗。

## <a name="how-does-personalizer-work"></a>個人化工具的運作方式

個人化工具會使用機器學習模型來探索要在內容中排名最高的動作。 您的用戶端應用程式會提供可能的動作清單，內含動作相關資訊，以及內容相關資訊，其中可能包含使用者、裝置等相關資訊。個人化工具可決定要採取的動作。 用戶端應用程式使用所選的動作後，它就會以回報分數的形式將意見反應提供給個人化工具。 收到意見反應後，個人化工具會自動更新用於未來排名的自有模型。 經過一段時間後，個人化工具將會定型一個模型，可根據每個內容的特徵來建議要在內容中選擇的最佳動作。

## <a name="how-do-i-use-the-personalizer"></a>如何使用個人化工具？

![使用個人化工具來選擇要對使用者顯示的影片](media/what-is-personalizer/personalizer-example-highlevel.png)

1. 選擇您應用程式中要進行個人化的體驗。
1. 在 Azure 入口網站中建立及設定個人化服務的執行個體。 每個執行個體都是個人化工具迴圈。
1. 使用 SDK，以使用者相關資訊 (「功能」  ) 和內容 (「動作」  ) 呼叫個人化工具。 使用個人化工具前，您不需要先提供已清理、標記的資料。 
1. 在用戶端應用程式中，向使用者顯示個人化工具所選取的動作。
1. 使用 SDK 將意見反應提供給個人化工具，指出是否使用者已選取個人化工具的動作。 這是「回報分數」  ，通常介於 -1 與 1 之間。
1. 在 Azure 入口網站檢視分析，以評估系統的運作情況以及您的資料對個人化有何幫助。

## <a name="where-can-i-use-personalizer"></a>個人化工具可以應用在何處？

例如，用戶端應用程式可以將個人化工具新增至：

* 將要在新聞網站上精選的文章個人化。    
* 將廣告放在網站上最佳的位置。
* 在購物網站上顯示個人化的「推薦項目」。
* 建議使用者介面項目，例如要套用至特定相片的篩選條件。
* 為聊天 Bot 選擇要用來釐清使用者意圖或提出建議動作的回應。
* 將使用者應該在商務流程中執行的後續建議排列優先順序。

個人化工具不是用來保存和管理使用者設定檔資訊的服務，也不是用來記錄個別使用者的喜好設定或歷程記錄。 個人化工具會學習每個互動的單一模型內容動作特徵，以在類似特徵出現時能得到最大報酬。 

## <a name="personalization-for-developers"></a>適用於開發人員的個人化

個人化工具服務有兩個 API：

* 傳送要個人化的使用者相關資訊 (「功能」  ) 和內容 (_動作_)。 個人化工具會以排名最高的動作回應。
* 以通常介於 0 與 1 (上一節表示 -1 與 1) 之間的數字，將有關排名方式的意見反應傳送給個人化工具。 

![個人化事件的基本序列](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>後續步驟

* [個人化工具有哪些新功能？](whats-new.md)
* [個人化工具如何運作？](how-personalizer-works.md)
* [什麼是增強式學習？](concepts-reinforcement-learning.md)
* [了解排名要求的功能和動作](concepts-features.md)
* [了解如何判斷獎勵要求的分數](concept-rewards.md)
* [使用互動式示範](https://personalizationdemo.azurewebsites.net/)
