---
title: 聲場模擬的設計概念
titlesuffix: Azure Cognitive Services
description: 這個概念的概觀 」 說明專案樂器包含完善的設計程序的原音模擬的方式。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 4a1a0b15da091a1c020eb132f6b14b9ee14d334c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316058"
---
# <a name="project-acoustics-design-process-concepts"></a>專案樂器設計程序概念

這個概念的概觀 」 說明專案樂器音效的設計程序所包含的實體的原音模擬。

## <a name="sound-design-with-audio-dsp-parameters"></a>完善的設計，具有音訊 DSP 參數

3D 的互動式標題達到使用音訊的數位訊號處理 (DSP) 區塊中的音訊引擎裝載其特定音效。 這些區塊的範圍從簡單的混合，以回響、 回應、 延遲、 等化、 壓縮和限制，和其他效果的複雜度。 選取、 排列，以及設定這些效果的參數是音效的設計工具，讓建置達成美觀和玩遊戲的目標體驗的音訊圖形的責任。

在互動式的標題，因為在 3D 空間中，移動的音效和接聽程式如何這些參數適應多變的條件？ 音效的設計工具通常會排列在空間內設計用來觸發程序來達成回響效果中的變更，比方說，或在混合中的 duck 聲音的接聽程式從場景的某一部分移到另一個參數變更的磁碟區。 樂器系統也會提供，可以自動化某些部分這些效果。

3D 項目使用光線和 kinematic 物理系統物理打轉，但設計工具調整，以達到混合的深度和玩遊戲的目標。 視覺化設計工具不會設定個別像素值，但而調整的 3D 模型、 教材及亮傳輸系統的所有實體為基礎來權衡視覺美術設計和 CPU 成本。 什麼是音訊的對等程序？ 專案樂器是探勘這個問題的第一個步驟。 第一次我們等等會談到傳輸空間，以透過 acoustical 能源的意義。

![殘響器區域與螢幕擷取畫面的 AltSpace 場景](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>脈衝回應：Acoustically 連接空間中的兩個點

如果您熟悉音訊的設計，您可能熟悉原音脈衝回應。 原音脈衝響應建立之傳輸的接聽程式的聲音來自來源的模型。 因此脈衝響應可以擷取每個特別有趣的房間樂器阻擋等回響。 脈衝回應也會有某些功能強大的屬性，可讓音訊 DSP 效果來調整。 一起加入兩個音訊訊號及處理脈衝響應與提供套用脈衝響應個別以每個訊號，並將結果與相同的結果。 柔和式傳播和脈衝回應也不需依賴處理，只有在建立模型，在場景的音訊及來源與接聽程式的位置。 簡單地說，脈衝響應抽取音效傳播場景的影響。

脈衝響應會擷取每個房間原音特別有趣，我們可以將它套用至音訊有效率地使用篩選條件，和我們可以取得脈衝回應從度量或模擬。 但是如果我們不很想要以完全符合物理樂器，但而塑造以符合場景的情感的需求？ 但更像素值，例如脈衝響應是剛數以千計的數字的清單，如何可以我們可能是調整以符合審美需求嗎？ 如果想要有阻擋/障礙物，而異的順暢時傳遞透過放不然就是障礙，我們要獲得順暢的效果脈衝回應次數？ 如果來源移動的速度嗎？ 我們該如何插入？

聽起來不用於樂器互動式標題中的某些層面的模擬和脈衝回應。 不過，我們仍然可以建置如果我們可以從模擬連接我們脈衝回應，使用我們熟悉的音訊 DSP 效果參數支援設計工具調整音訊的傳輸系統。

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>連接的模擬，以使用參數的音訊 DSP

脈衝響應包含每個有趣的 (而每個認為不重要) acoustical 的效果。 音訊 DSP 區塊，當其參數的設定正確，可以轉譯 acoustical 特別有趣。 使用 acoustical 模擬磁碟機來自動化 3D 場景中的音訊傳輸音訊 DSP 區塊是只有幾測量脈衝響應的音訊 DSP 參數。 某些常見和重要 acoustical 效果包括阻擋、 障礙、 portalling 和回響充分了解這個度量單位。

但是，如果模擬直接連接到音訊 DSP 參數，其中是設計工具調整？ 我們未獲得什麼？ 嗯，我們可以回，捨棄脈衝回應保留幾個 DSP 參數以大量的記憶體。 並提供一些的能力設計工具，透過最後的結果，我們只需要找到方法來插入模擬與音訊 DSP 之間的設計工具。

![圖形與樣式化的脈衝響應使用重疊的參數](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>完善的設計，方法是轉換從模擬的音訊 DSP 參數

請考慮您太陽眼鏡對世界的檢視效果。 亮的一天，眼鏡可以減少發亮更舒適的項目。 您可能無法完全顯示任何項目。 眼鏡不需要一定程度的亮度設定中所有的情況下，只是使較暗的所有項目。

如果我們使用模擬來驅動我們的音訊 DSP 使用會受阻擋和回響參數時，我們可以加入篩選之後的模擬器，以調整 DSP '會看到' 的參數。 篩選不會強制執行特定層級的阻擋物或殘響器結尾長度更像太陽眼鏡不挪出每個相同的亮度。 篩選器可能只會使 occlude 小於每個 occluder。 或 occlude 更多。 藉由新增並調整一個 '冶煉' 阻擋參數的篩選條件，大型、 開啟室還是很阻擋效果，雖然放會增加從媒體的強式阻擋效果，同時保留有效的平滑轉換可提供模擬。

在此範例中，設計工具的工作會從選擇柔和式參數，每種情況下，選取與調整套用至來自模擬最重要的 DSP 參數的篩選變更。 它從窄的考量設定順利地轉換到較高的考量，阻擋和回響效果的濃度和混合中的來源存在的權限提高設計工具的活動。 當然，這種情況要求，一個篩選條件永遠可使用時回到只在特定情況下，選擇特定來源的 DSP 參數。

## <a name="sound-design-in-project-acoustics"></a>在專案樂器音效設計

專案樂器套件整合，每個以上所述的元件： 一個模擬器，編碼器會擷取參數，並建立樂器資產、 音訊 DSP 和選取的篩選器。 與專案樂器音效設計需要選擇調整阻擋和回響參數衍生自模擬，而且套用至音訊 DSP，遊戲的編輯器及音訊引擎內公開之動態控制項的篩選參數。

## <a name="next-steps"></a>後續步驟
* 嘗試使用您建立設計典範[Unity 專案樂器快速入門](unity-quickstart.md)或[Unreal 專案樂器快速入門](unreal-quickstart.md)
* 瀏覽[專案樂器設計控制項的 Unity](unity-workflow.md)或[Unreal 專案樂器設計的控制項](unreal-workflow.md)

