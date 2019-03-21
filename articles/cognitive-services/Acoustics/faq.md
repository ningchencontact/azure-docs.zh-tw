---
title: 專案樂器常見問題集
titlesuffix: Azure Cognitive Services
description: 本頁面提供 Project Acoustics 相關常見問題的解答，其中包括下載指示和製作流程。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: c43c81d42a39bda504b02eb6c053a16a2cf53aec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58138043"
---
# <a name="project-acoustics-frequently-asked-questions"></a>專案樂器常見問題集

## <a name="what-is-project-acoustics"></a>何謂 Project Acoustics？

外掛程式的專案樂器套件是一種樂器系統會計算在執行階段，類似於靜態光源之前音波行為。 雲端可執行繁重的波動物理學計算，因此執行階段 CPU 成本很低。  

## <a name="where-can-i-download-the-plugin"></a>我可以從哪裡下載外掛程式？

您可以下載從專案樂器[專案樂器 Download Center page](https://www.microsoft.com/en-us/download/details.aspx?id=57346)。

## <a name="does-project-acoustics-support-x-platform"></a>支援專案樂器<x>平台？

根據客戶需求的發展專案樂器平台支援。 請與我們連絡上[專案樂器論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)詢問其他平台的支援。

## <a name="is-azure-used-at-runtime"></a>在執行階段是否會使用 Azure？

否，只有在場景設定的計算前階段期間，才會使用雲端整合。
 
## <a name="what-is-simulation-input"></a>什麼是模擬輸入？ 

模擬輸入係指您的 3D 場景、虛擬環境或遊戲關卡。 Project Acoustics 會執行可仔細建立聲音物理特性模型的 3D 體積波動模擬，包括平滑的遮蔽和散射。
 
## <a name="what-is-the-runtime-cost"></a>有哪些執行階段成本？

聲場的每一畫面每一來源大約會耗用 0.01% 的 CPU。 RAM 使用量則取決於場景大小，範圍從 10 到 100 MB。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>我是否需要簡化關卡幾何？ 控制三角面計數？ 讓網格防水？

沒有。 此系統會直接內嵌詳細的關卡幾何。 系統會將其體素化來進行內部處理。
 
## <a name="whats-in-the-runtime-lookup-table"></a>什麼是執行階段查閱資料表？

ACE 檔案包含是柔和式參數之間多個來源和接聽程式位置的配對，以及用於參數內插補點 voxelized 場景幾何的資料表。
 
## <a name="can-project-acoustics-handle-moving-sources"></a>專案樂器可以處理移動的來源？

是，專案樂器會查閱查閱資料表，並更新每個刻度，音訊 DSP，因此它可以處理移動來源與接聽程式。
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>專案樂器可以處理動態幾何？ 關閉中的門？ 被吹走的牆面？

沒有。 聲場參數是根據遊戲關卡的靜態狀態預先計算的。 離開樂器，超出的媒體櫃門幾何，然後將套用其他阻擋易損壞的狀態為基礎的建議，並使用可移動的遊戲物件建立技術。
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>專案樂器是否使用原音資料？

是。 它會從您關卡中的實體材質名稱挑選材質來決定吸收性。
 
## <a name="what-do-the-probes-represent"></a>「探查」代表什麼？

探查是針對可能之玩家位置進行的取樣。 每個探查代表一個源自探查位置的個別場景波動模擬。 在執行階段，會從附近的探查位置插補聆聽者位置的聲場參數。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>為什麼要在雲端進行那麼多的計算？ 它能為我提供什麼？

Project Acoustics 會將每個架構層面納入考量，即使針對極複雜的虛擬環境，也能提供既準確又可靠的聲場參數。 它提供順暢的阻擋物和障礙物的繪圖的磁碟區的動態殘響器變化而不需要手動工作。 所有這些都可在執行階段減輕 CPU 的負擔。

## <a name="what-exactly-happens-during-baking"></a>進行「製作」時，實際會發生什麼事？

製作所組成的中心位於每個接聽程式探查 cuboid 模擬區域的原音 wave 模擬。

## <a name="next-steps"></a>後續步驟
* 請嘗試[專案樂器 Unity 範例內容](unity-quickstart.md)或[Unreal 範例內容](unreal-quickstart.md)

