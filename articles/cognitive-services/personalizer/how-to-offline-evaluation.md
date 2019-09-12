---
title: 離線評估-個人化工具
titleSuffix: Azure Cognitive Services
description: 了解如何透過離線評估來分析您的學習迴圈
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: a157b36ad2b4f5a7f2e50a9609d8acd621efd5a8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884454"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>使用離線評估來分析您的學習迴圈

了解如何完成離線評估並了解結果。

離線評估可讓您測量個人化工具的有效性 (相較於您應用程式的預設行為)，了解哪些功能對個人化最有貢獻，以及自動探索新的機器學習設定。

閱讀[離線評估](concepts-offline-evaluation.md)進一步了解。


## <a name="prerequisites"></a>必要條件

* 已設定的個人化工具迴圈
* 個人化工具迴圈必須有代表性的資料量-作為約略，我們建議記錄中至少有50000個事件，以取得有意義的評估結果。 (選擇性) 您可能先前也已匯出「學習原則」檔案，以便在相同評估中進行比較和測試。

## <a name="steps-to-start-a-new-offline-evaluation"></a>啟動新的離線評估的步驟

1. 在  [Azure 入口網站](https://azure.microsoft.com/free/)中，找出您的個人化資源。
1. 在 Azure 入口網站中，移至 [**評估**] 區段，然後選取 [**建立評估**]。
    ![在 Azure 入口網站中，移至 [評估] 區段，然後選取 [建立評估]。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 選取下列設定：

    * 評估名稱
    * 開始和結束日期-這些是過去的日期，可指定要在評估中使用的資料範圍。 如[資料保留](how-to-settings.md)設定所指定，此資料必須存在記錄中。
    * 優化探索設定為 **[是]**

    ![選擇離線評估設定](./media/offline-evaluation/create-an-evaluation-form.png)

1. 選取 **[確定]** 以開始評估。 

## <a name="results"></a>結果

評估可能需要很長的執行時間，這取決於要處理的資料量、要比較的學習原則數目、是否已要求最佳化。

完成後，您可以從評估清單中選取評估。 

學習原則的比較包括：

* **線上原則**：個人化工具中目前使用的學習原則
* **基準**：應用程式的預設值 (由排名呼叫中傳送的第一個動作所決定)
* **隨機原則**：虛構排名行為，其一律會從所提供的動作傳回隨機選擇的動作。
* **自訂原則**：開始評估時上傳的額外學習原則。
* **最佳化原則**：若開始評估時採用探索最佳化原則的選項，則也會比較最佳化原則，而且您能夠下載該原則或使其成為線上學習原則，並取代目前的學習原則。

![離線評估設定的結果圖表](./media/offline-evaluation/evaluation-results.png)

動作和內容的[功能](concepts-features.md)有效性。

## <a name="next-steps"></a>後續步驟

* 了解[離線評估的運作方式](concepts-offline-evaluation.md)。
