---
title: 聲場專案製作解決方案
titlesuffix: Azure Cognitive Services
description: 此概念總覽說明烘焙聲場時的粗略與精細解析度之間的差異。
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: kylsto
ROBOTS: NOINDEX
ms.openlocfilehash: e237a010bfecc038816c1eedf94e2f94b1e12472
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704864"
---
# <a name="project-acoustics-bake-resolution"></a>聲場專案製作解決方案
此概念總覽說明烘焙聲場時的粗略與精細解析度之間的差異。 您可以在烘焙工作流程的探查步驟期間選擇此設定。

## <a name="Coarse-vs-Fine-Resolution"></a>粗略與精細解析度的比較

[Coarse] \(粗略\) 和 [Fine] \(精細\) 解析度設定之間的唯一差異在於模擬的執行頻率。 [Fine] \(精細\) 所使用的頻率為 [Coarse] \(粗略\) 的兩倍。 這對聲場模擬有一些含意:

* [Coarse] \(粗略\) 的波長為 [Fine] \(精細\) 的兩倍，因此體素是兩倍大。
* 由於聲場模擬時間與體素大小直接相關，因此 [Coarse] \(粗略\) 模擬比 [Fine] \(精細\) 模擬快 16 倍。
* 不能模擬小於體素大小的入口網站 (例如, 門或 windows)。 粗略的設定可能會導致某些較小的入口網站無法模擬;因此, 它們不會在執行時間傳遞音效。 您可以檢視體素來查看是否會發生這種情況。
* 模擬頻率越低，在角落和邊緣周圍產生的繞射就越少。
* 聲音來源不能位於「已填滿」的體素內 (也就是包含幾何的體素)。 這會導致無音效。 較難以放置音效來源, 因此它們不會在較大的體素範圍內, 而不是使用 [微調] 設定。
* 較大的體素侵入門戶的部分也較大，如下所示。 第一張圖是使用 [Coarse] \(粗略\) 解析度來建立的，第二張圖是相同的門口，但使用 [Fine] \(精細\) 解析度。 如紅色標記所示，使用 [Fine] \(精細\) 設定時，侵入門口的程度較小。 藍線是幾何所定義的門口，紅線則是體素大小所定義的有效聲場門戶。 此入侵狀況在指定的情況下會如何表現，完全取決於體素與門戶幾何的對齊方式，而這又取決於場景中物件的大小和位置。

![Unreal 中填滿門口的粗略體素螢幕擷取畫面](media/unreal-coarse-bake.png)

![Unreal 中門口精細體素的螢幕擷取畫面](media/unreal-fine-bake.png)

## <a name="next-steps"></a>後續步驟

使用我們的[Unreal](unreal-baking.md)或[Unity](unity-baking.md)外掛程式, 自行嘗試粗略且精細的解決設定。
