---
title: 聲場專案常見問題
titlesuffix: Azure Cognitive Services
description: 本頁面提供 Project Acoustics 相關常見問題的解答，其中包括下載指示和製作流程。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: a965fc020c5c534616459ad661b71ac67dbc2425
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704801"
---
# <a name="project-acoustics-frequently-asked-questions"></a>聲場專案常見問題

## <a name="what-is-project-acoustics"></a>何謂 Project Acoustics？

外掛程式的聲場專案套件是聲場系統, 會在執行時間之前計算音效行為, 類似于靜態光源。 雲端可執行繁重的波動物理學計算，因此執行階段 CPU 成本很低。  

## <a name="where-can-i-download-the-plugin"></a>我可以從哪裡下載外掛程式？

您可以下載[聲場專案 Unity 外掛程式](https://www.microsoft.com/download/details.aspx?id=57346)或[聲場專案 Unreal 外掛程式](https://www.microsoft.com/download/details.aspx?id=58090)。

## <a name="does-project-acoustics-support-ltxgt-platform"></a>聲場專案支援&lt;x&gt;平臺嗎？

聲場專案平臺支援是根據客戶需求而演變的。 請洽詢[聲場專案問題論壇](https://github.com/microsoft/ProjectAcoustics/issues), 詢問其他平臺的支援。

## <a name="is-azure-used-at-runtime"></a>在執行階段是否會使用 Azure？

否，只有在場景設定的計算前階段期間，才會使用雲端整合。
 
## <a name="what-is-simulation-input"></a>什麼是模擬輸入？ 

模擬輸入係指您的 3D 場景、虛擬環境或遊戲關卡。 Project Acoustics 會執行可仔細建立聲音物理特性模型的 3D 體積波動模擬，包括平滑的遮蔽和散射。
 
## <a name="what-is-the-runtime-cost"></a>有哪些執行階段成本？

聲場的每一畫面每一來源大約會耗用 0.01% 的 CPU。 RAM 使用量則取決於場景大小，範圍從 10 到 100 MB。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>我是否需要簡化關卡幾何？ 控制三角面計數？ 讓網格防水？

資料分割 此系統會直接內嵌詳細的關卡幾何。 系統會將其體素化來進行內部處理。
 
## <a name="whats-in-the-runtime-lookup-table"></a>什麼是執行階段查閱資料表？

ACE 檔案包含多個來源和接聽程式位置配對之間的聲場參數資料表, 以及用於參數插補的體素化場景 geometry。
 
## <a name="can-project-acoustics-handle-moving-sources"></a>聲場專案可以處理移動的來源嗎？

是, 聲場專案查閱查閱資料表, 並更新每個刻度上的音訊 DSP, 讓它可以處理移動的來源和接聽程式。
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>聲場專案可以處理動態幾何嗎？ 關閉中的門？ 被吹走的牆面？

資料分割 聲場參數是根據遊戲關卡的靜態狀態預先計算的。 我們建議您將門幾何離開聲場, 然後使用已建立的技術, 根據易損壞和可移動遊戲物件的狀態來套用額外的遮蔽。
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>聲場專案使用聲場材質嗎？

是的。 它會從您關卡中的實體材質名稱挑選材質來決定吸收性。
 
## <a name="what-do-the-probes-represent"></a>「探查」代表什麼？

探查是針對可能之玩家位置進行的取樣。 每個探查代表一個源自探查位置的個別場景波動模擬。 在執行階段，會從附近的探查位置插補聆聽者位置的聲場參數。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>為什麼要在雲端進行那麼多的計算？ 它能為我提供什麼？

Project Acoustics 會將每個架構層面納入考量，即使針對極複雜的虛擬環境，也能提供既準確又可靠的聲場參數。 它提供平滑的遮蔽和障礙物, 以及動態的回音變化, 而不需要手動繪製磁片區的工作。 所有這些都可在執行階段減輕 CPU 的負擔。

## <a name="what-exactly-happens-during-baking"></a>進行「製作」時，實際會發生什麼事？

製作包含以立體模擬區域的聲場模擬, 以每個接聽程式探查為中心。

## <a name="next-steps"></a>後續步驟
* 嘗試[聲場專案 Unity 範例內容](unity-quickstart.md)或[Unreal 範例內容](unreal-quickstart.md)

