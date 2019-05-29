---
title: 探索 - 個人化工具
titleSuffix: Azure Cognitive Services
description: 透過探索，即使使用者行為變更，個人化工具還是能夠繼續提供良好的結果。 選擇探索設定是一項業務決策，用來決定要探索多少比例的使用者互動，才能改善模型。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: 0e11dd962f4cc1ff9b1938b309a1b405dbf20232
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607021"
---
# <a name="exploration-and-exploitation"></a>探索 (Exploration) 和利用 (Exploitation)

透過探索，即使使用者行為變更，個人化工具還是能夠繼續提供良好的結果。

當個人化工具收到排序呼叫時，其傳回的 RewardActionID 可能是：
* 使用「利用」來根據目前的機器學習模型，以最可能的使用者行為做比對。
* 使用「探索」，不以排名中機率最高的動作最比對。

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
個人化工具目前使用稱為 "epsilon greedy"  的演算法來進行探索。 

## <a name="choosing-an-exploration-setting"></a>選擇探索設定

在 Azure 入口網站的個人化工具 [設定]  頁面中，您可以設定要用於探索的流量百分比。 此設定會決定執行探索的排名呼叫百分比。 

個人化工具會決定是否要探索或利用每個排名呼叫上的此機率。 這有別於某些 A/B 架構中的行為，也就是鎖定特定使用者識別碼上的處理方式。

## <a name="best-practices-for-choosing-an-exploration-setting"></a>選擇探索設定的最佳做法

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

選擇探索設定是一項業務決策，用來決定要探索多少比例的使用者互動，才能改善模型。 

若設定為零，則會否定許多個人化工具的優點。 透過此設定，個人化工具不會使用使用者互動來探索更好的使用者互動。 這會導致模型停滯、漂移，並在最終造成較低的效能。

設定過高的值會不利於使用者行為的學習。 將其設定為 100% 表示不斷隨機選取，而任何從使用者身上學到的行為皆不會影響結果。

無論您使否看到個人化工具正在使用探索或利用，請絕對不要隨之變更應用程式行為。 這會導致學習偏差，最終將會降低潛在效能。

## <a name="next-steps"></a>後續步驟

[增強式學習](concepts-reinforcement-learning.md) 