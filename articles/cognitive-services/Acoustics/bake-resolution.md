---
title: 專案樂器製作解析
titlesuffix: Azure Cognitive Services
description: 這個概念的概觀說明粗略與沒問題的解決方式，同時將備份樂器之間的差異。
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 04/05/2019
ms.author: KyleStorck
ms.openlocfilehash: 7dbf63ba39c5dcdebb363cfc37a45f0216a07497
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335441"
---
# <a name="project-acoustics-bake-resolution"></a>專案樂器製作解析
這個概念的概觀說明粗略與沒問題的解決方式，同時將備份樂器之間的差異。 您選擇此烘焙維度的工作流程的探查步驟期間設定。

## <a name="Coarse-vs-Fine-Resolution"></a>粗略與精細解析度的比較

[Coarse] \(粗略\) 和 [Fine] \(精細\) 解析度設定之間的唯一差異在於模擬的執行頻率。 [Fine] \(精細\) 所使用的頻率為 [Coarse] \(粗略\) 的兩倍。 這是柔和式模擬上有幾個隱含意義：

* [Coarse] \(粗略\) 的波長為 [Fine] \(精細\) 的兩倍，因此體素是兩倍大。
* 由於聲場模擬時間與體素大小直接相關，因此 [Coarse] \(粗略\) 模擬比 [Fine] \(精細\) 模擬快 16 倍。
* 無法模擬入口網站 （例如，機門或 windows） 小於 voxel 大小。 粗略的設定可能導致一些要模擬不; 這些較小的入口網站因此，不會通過音效透過在執行階段。 您可以檢視體素來查看是否會發生這種情況。
* 模擬頻率越低，在角落和邊緣周圍產生的繞射就越少。
* 「 區域分布 「 voxels (也就是 voxels 包含幾何) 內找不到聲音的來源。 這會不導致任何聲音。 它很難將音效來源，因此它們不是在較大的 voxels 粗略比使用 [正常] 設定時的內部。
* 較大的體素侵入門戶的部分也較大，如下所示。 第一張圖是使用 [Coarse] \(粗略\) 解析度來建立的，第二張圖是相同的門口，但使用 [Fine] \(精細\) 解析度。 如紅色標記所示，使用 [Fine] \(精細\) 設定時，侵入門口的程度較小。 藍線是幾何所定義的門口，紅線則是體素大小所定義的有效聲場門戶。 此入侵狀況在指定的情況下會如何表現，完全取決於體素與門戶幾何的對齊方式，而這又取決於場景中物件的大小和位置。

![Unreal 中填滿門口的粗略體素螢幕擷取畫面](media/unreal-coarse-bake.png)

![Unreal 中門口精細體素的螢幕擷取畫面](media/unreal-fine-bake.png)

## <a name="next-steps"></a>後續步驟

使用自行試用粗略和順利解析設定我們[Unreal](unreal-baking.md)或是[Unity](unity-baking.md)外掛程式。