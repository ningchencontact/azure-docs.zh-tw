---
title: 搭配 Unreal 的聲場專案快速入門
titlesuffix: Azure Cognitive Services
description: 使用範例內容對 Unreal 和 Wwise 中的聲場專案設計控制項進行實驗，並部署至 Windows 桌面。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: a78df2d4d84487399da10ca722550639a92e71bf
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798132"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>聲場專案 Unreal/Wwise 快速入門
在本快速入門中，您將會使用針對 Unreal Engine 和 Wwise 所提供的範例內容，對聲場專案設計控制項進行實驗。

軟體需求：
* [Unreal Engine](https://www.unrealengine.com/) 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6

## <a name="download-the-sample-package"></a>下載範例套件
下載[聲場專案 Unreal + Wwise 範例套件](https://www.microsoft.com/download/details.aspx?id=58090) \(英文\)。 範例套件包含 Unreal Engine 專案、適用於該 Unreal 專案的 Wwise 專案，以及聲場專案 Wwise 外掛程式。

## <a name="set-up-the-project-acoustics-sample-project"></a>設定聲場專案範例專案
若要設定聲場專案 Unreal/Wwise 範例專案，您必須先將聲場專案外掛程式安裝至 Wwise 中。 然後將 Wwise 二進位檔部署至 Unreal 專案，並調整 Wwise 的 Unreal 外掛程式以支援聲場專案。

### <a name="install-the-project-acoustics-wwise-plugin"></a>安裝聲場專案 Wwise 外掛程式
開啟 Wwise 啟動器，然後在 [Plugins]  \(外掛程式\) 索引標籤的 [Install New Plugins]  \(安裝新外掛程式\) 之下，選取 [Add From Directory]  \(從目錄新增\)。 選擇包含在您所下載之套件中的 `AcousticsWwisePlugin\ProjectAcoustics` 目錄。

![顯示 [安裝 Wwise 外掛程式] 選項的 Wwise 啟動器螢幕擷取畫面](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>將 Wwise 二進位檔新增至聲場專案 Unreal 範例專案
從 Wwise 啟動器，按一下 [Unreal Engine]  索引標籤，然後按一下 [Recent Unreal Engine Projects]  \(最近使用的 Unreal Engine 專案\) 旁邊的漢堡功能表，並選取 [Browse for project]  \(瀏覽專案\)。 開啟 `AcousticsSample\AcousticsGame\AcousticsGame.uproject` 套件中的範例 Unreal 專案 `.uproject` 檔案。

![Wwise 啟動器 Unreal 索引標籤的螢幕擷取畫面](media/wwise-unreal-tab.png)

然後，按一下聲場專案範例專案旁邊的 [Integrate Wwise in Project]  \(在專案中整合 Wwise\)。

![顯示聲場遊戲 Unreal 專案的 Wwise 啟動器螢幕擷取畫面](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>擴充 Wwise 的 Unreal 外掛程式功能
聲場專案 Unreal 外掛程式需要從 Wwise Unreal 外掛程式 API 公開額外的行為。 請執行聲場專案 Unreal 外掛程式所提供的批次檔，以將這些修改作業自動化：
* 在 `AcousticsGame\Plugins\ProjectAcoustics\Resources` 內，執行 `PatchWwise.bat`。

    ![[Windows 檔案總管] 視窗的螢幕擷取畫面，其中顯示可修補 Wwise 專案的指令碼](media/patch-wwise-script.png)

* 如果您尚未安裝 DirectX SDK，則根據您使用的 Wwise 版本，您可能需要將 `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs` 中包含 `DXSDK_DIR` 的那一行註解化：

    ![顯示已註解排除 DXSDK 的程式碼編輯器螢幕擷取畫面](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>開啟 Unreal 專案。 
系統會要求您重新建置模組，請按一下 [Yes] \(是\)。

>如果開啟專案因建置失敗而失敗，請檢查是否已將聲場專案 Wwise 外掛程式安裝至和用於聲場專案範例專案相同的 Wwise 版本。

>如果未使用 [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6，則必須先重新產生音效庫，才能在專案範例中播放音訊。

## <a name="experiment-with-project-acoustics-design-controls"></a>對聲場專案設計控制項進行實驗
按一下 Unreal 編輯器中的 [Play] \(進行遊戲\) 按鈕來聆聽場景聲音。 在電腦上使用 W、A、S、D 與滑鼠來四處移動。 若要查看鍵盤快速鍵以取得更多控制項，請按下 **F1**。 以下是可嘗試的一些設計活動：

### <a name="modify-occlusion-and-transmission"></a>修改遮蔽和傳輸
每個 Unreal 聲音動作項目上都有每個來源的聲場專案設計控制項：

![Unreal 編輯器聲場設計控制項的螢幕擷取畫面](media/demo-scene-sound-source-design-controls.png)

如果 [Occlusion]  \(遮蔽\) 的乘數大於 1 (預設值為 1)，遮蔽便會被放大。 將它設定為小於 1 會使遮蔽效果更細微。

若要啟用穿牆的傳輸，請將 [Transmission (dB)]  \(傳輸 (dB)\) 滑桿從其最低位置移開。 

### <a name="modify-wetness-for-a-source"></a>修改來源的濕潤度
若要變更濕潤度隨距離變更的程度，請使用 [Perceptual Distance Warp]  \(感知距離變形\)。 聲場專案會透過模擬計算整個空間的濕潤層級，此數值會因距離而緩和變化，並為玩家提供感知距離的線索。增加距離變形將會提升與距離相關的濕潤層級，以放大此效果。 變形值小於 1 會使以距離為基礎的殘響變化變得更加細微。 這個效果也可以透過調整 [Wetness (dB)]  \(濕潤度 (dB)\) 來以更細微的調整幅度加以調整。

透過調整 [Decay Time Scale]  \(衰減時間刻度\) 來調整整個空間的衰減時間。 讓我們以模擬結果為 1.5 秒的衰減時間作為例子。 將 [Decay Time Scale]  \(衰減時間刻度\) 設為 2，會使系統將 3 秒的衰減時間套用至來源。

### <a name="modify-distance-based-attenuation"></a>修改以距離為基礎的衰減
聲場專案 Wwise 混音器外掛程式會遵循 Wwise 所內建，以每個來源距離為基礎的衰減。 變更此曲線將會變更乾燥路徑層級。 聲場專案外掛程式將會調整濕潤層級，以維持模擬和設計控制項所指定的濕潤-乾燥混音。

![Wwise 衰減曲線面板的螢幕擷取畫面，其中顯示在模擬界限前朝向零的衰減](media/demo-sounds-attenuation.png)

聲場專案會在以每個模擬玩家位置為中心的「模擬區域」方塊中執行計算。 範例套件中的聲場資產是以 45 公尺的模擬區域半徑進行聲場模擬，且衰減被設計為在抵達 45 公尺之前便會降至 0。 雖然此降低並非嚴格的要求，您應該留意到只有位於聆聽者 45 公尺內的幾何才會遮蔽聲音。

## <a name="next-steps"></a>後續步驟
* [整合聲場專案](unreal-integration.md)外掛程式至您的 Unreal 專案
* 為您自己的製作項目[建立 Azure 帳戶](create-azure-account.md)


