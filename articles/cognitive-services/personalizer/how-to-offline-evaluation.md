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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f14403422e2c783d75634bb929d8c2130bd505b6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663882"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>如何透過離線評估來分析您的學習迴圈


了解如何完成離線評估並了解結果。

離線評估可讓您測量個人化工具的有效性 (相較於您應用程式的預設行為)，了解哪些功能對個人化最有貢獻，以及自動探索新的機器學習設定。

閱讀[離線評估](concepts-offline-evaluation.md)進一步了解。


## <a name="prerequisites"></a>必要條件

1. 您必須已設定個人化工具迴圈
1. 個人化工具迴圈在其記錄中必須有至少 50,000 個事件，以提供有意義的評估結果。

(選擇性) 您可能先前也已匯出「學習原則」  檔案，以便在相同評估中進行比較和測試。

## <a name="steps-to-start-a-new-offline-evaluation"></a>開始新離線評估的步驟

1. 在 Azure 入口網站中找出您的個人化迴圈資源。
1. 巡覽至 [評估] 區段。
1. 按一下 [新增評估]。
1. 選取離線評估的開始和結束日期。 這些都是過去的日期，可指定要在評估中使用的資料範圍。 如[資料保留](how-to-settings.md)設定所指定，此資料必須存在記錄中。
1. (選擇性) 您可以上傳您自己的學習原則。 
1. 指定個人化工具是否應該根據在這段期間內觀察到的使用者行為，建立最佳化學習原則。
1. 開始評估

## <a name="results"></a>結果

評估可能需要很長的執行時間，這取決於要處理的資料量、要比較的學習原則數目、是否已要求最佳化。

完成後，您可以看到下列結果：

1. 學習原則的比較，其中包括：
    * **線上原則**：個人化工具中目前使用的學習原則
    * **基準**：應用程式的預設值 (由排名呼叫中傳送的第一個動作所決定)
    * **隨機原則**：虛構排名行為，其一律會從所提供的動作傳回隨機選擇的動作。
    * **自訂原則**：開始評估時上傳的額外學習原則。
    * **最佳化原則**：若開始評估時採用探索最佳化原則的選項，則也會比較最佳化原則，而且您能夠下載該原則或使其成為線上學習原則，並取代目前的學習原則。

1. 動作和內容的[功能](concepts-features.md)有效性。


## <a name="more-information"></a>更多資訊

* 了解[離線評估的運作方式](concepts-offline-evaluation.md)。