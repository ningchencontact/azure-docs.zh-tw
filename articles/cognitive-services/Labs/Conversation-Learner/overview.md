---
title: 什麼是對話學習模組？ - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 了解對話學習模組及其運作方式。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c46ffe2076d4b1491a3b27958dfbf5ed09115eaa
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221279"
---
# <a name="what-is-conversation-learner"></a>什麼是對話學習模組？

對話學習模組可讓您建置並教導對話介面從範例互動中學習。 

不同於傳統方法，對話學習模組會考慮對話的端對端內容，以改善回應，並達到更有吸引力的使用者經驗。 Conversation Learner 橫跨眾多工作導向的使用情況，可在幕後運用機器學習來改善聊天機器人和智慧型代理程式，避免讓使用者不耐煩，或造成額外的顧客服務成本，並促使互動更為直覺。

開發人員首先會輸入他們想要模擬的典型對話內容。 隨著更多對話的輸入，模型會持續學習。 模型運作良好時，聊天機器人即可部署到終端使用者。 對話學習模式會記錄使用者的對話，而且開發人員可以檢閱對話內容。 如果發現錯誤，開發人員即可進行立即修正，而且模型將重新訓練且立即可供使用。

這種方法可減少手動編寫對話控制邏輯，而且企業主或領域專家不需要先具備機器學習知識即可參與改善對話介面。 無論對話學習模組是部署到聊天機器人、智慧型裝置或智慧型代理程式，都可以快速取得新的技術、行為或能力，以快速改善其品質。 

對話學習模組可讓開發人員加快上市時間，並透過 Microsoft Bot 架構或對話通道各自的獨立基礎結構，促進多個對話通道的對話成功。

摘要及重點：

- 對話學習模組是建置工作導向的聊天機器人所採用的 AI 優先做法。

- 這運用端對端週期性類神經網路 (LSTM)，並且會直接從多次的對話範例學習。 

- 可讓設計人員、開發人員、商務使用者和客服中心工作人員建置和維護聊天機器人。 

- 提供以程式碼表達商務規則和常識的能力。

- 在教導工作階段期間，類神經網路模型將用來對於對話中的下一組預期動作進行評分。 聊天機器人開發人員可以接著選取正確的動作，並訓練網路提供適當的回應。
 
- 訓練完成後，開發人員可以使用使用者互動的記錄對話來修正聊天機器人回應，並重新訓練模型。 

- 可以呼叫領域特定和第三方 API 完成工作。

