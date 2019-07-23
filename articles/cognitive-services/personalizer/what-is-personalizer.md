---
title: 什麼是個人化工具？
titleSuffix: Azure Cognitive Services
description: 個人化工具是雲端式 API 服務，可讓您選擇最佳體驗來對使用者展現，進而從其即時行為中學習。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 286a19207236392367b924bea7e26e90fd0db8d5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253454"
---
# <a name="what-is-personalizer"></a>什麼是個人化工具？

Azure 個人化工具是雲端式 API 服務，可讓您選擇最佳體驗來對使用者展現，進而從其即時行為中學習。

* 提供您的使用者和內容相關資訊，並接收排名最高的動作以顯示您的使用者。 
* 使用個人化工具前，不需要清理和標記資料。
* 方便時，將意見反應提供給個人化工具。 
* 檢視即時分析。 
* 在大型資料科學工作中使用個人化工具來驗證現有的實驗。

## <a name="how-does-personalizer-work"></a>個人化工具的運作方式

個人化工具會使用機器學習模型來探索要在內容中排名最高的動作。 您的用戶端應用程式會提供可能的動作清單，內含動作相關資訊，以及內容相關資訊，其中可能包含使用者、裝置等相關資訊。個人化工具可決定要採取的動作。 用戶端應用程式使用所選的動作後，它就會以回報分數的形式將意見反應提供給個人化工具。 收到意見反應後，個人化工具會自動更新用於未來排名的自有模型。

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

## <a name="personalization-for-developers"></a>適用於開發人員的個人化

個人化工具服務有兩個 API：

* 傳送要個人化的使用者相關資訊 (「功能」  ) 和內容 (_動作_)。 個人化工具會以排名最高的動作回應。
* 以通常介於 0 與 1 (上一節表示 -1 與 1) 之間的數字，將有關排名方式的意見反應傳送給個人化工具。 

![個人化事件的基本序列](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>後續步驟

* [快速入門：以 C# 建立意見反應迴圈](csharp-quickstart-commandline-feedback-loop.md)
* [使用互動式示範](https://personalizationdemo.azurewebsites.net/)
