---
title: 搭配 Unity 的聲場專案快速入門
titlesuffix: Azure Cognitive Services
description: 使用範例內容對 Unity 中的聲場專案設計控制項進行實驗，並部署至 Windows 桌面。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: b4eedabbc47738eb2f5797ffd67a3e3ebc9529ca
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136216"
---
# <a name="project-acoustics-unity-quickstart"></a>聲場專案 Unity 快速入門
使用適用於 Unity 的聲場專案範例內容對模擬所支援的設計控制項進行實驗。

軟體需求：
* 適用於 Windows 的 [Unity 2018.2+](http://unity3d.com) \(英文\)
* [聲場專案範例內容套件](https://www.microsoft.com/en-us/download/details.aspx?id=57346) \(英文\)

範例套件中包含哪些內容？
* 具有幾何、聲音來源，以及遊戲控制項的 Unity 場景
* 聲場專案外掛程式 
* 適用於該場景並已完成聲場模擬的聲場資產

## <a name="import-the-sample-package"></a>匯入範例套件
將範例套件匯入至新的 Unity 專案。 
* 在 Unity 中，移至 [Assets] \(資產\) > [Import Package] \(匯入套件\) > [Custom Package] \(自訂套件\)

    ![匯入套件](media/import-package.png)  

* 選擇 **ProjectAcoustics.unitypackage**

如果您正在將套件匯入現有專案，請參閱 [Unity 整合](unity-integration.md)以取得額外步驟和注意事項。

## <a name="restart-unity"></a>重新啟動 Unity
聲場工具組的製作部分需要 .NET 4.x 指令碼處理執行階段版本。 套件匯入將會更新您的 Unity 播放器設定。 請重新啟動 Unity 來讓此設定生效。

您可以透過開啟 [Player Settings] \(玩家設定\)，確認此設定是否已生效：

![播放器設定](media/player-settings.png)

![.NET 4.5](media/net45.png)

## <a name="experiment-with-design-controls"></a>對設計控制項進行實驗
開啟 **ProjectAcousticsSample** 資料夾中的範例場景，然後按一下 Unity 編輯器中的 [Play] \(進行遊戲\) 按鈕。 使用 W、A、S、D 與滑鼠即可四處移動。 若要比較場景音效使用原音與不使用原音時的不同，請按下 **R** 按鈕直到重疊文字變成紅色並說出「原音：已停用」。 若要查看鍵盤快速鍵以取得更多控制項，請按下 **F1**。 若要使用控制項，您也可以按一下滑鼠右鍵來選取要執行的動作，然後按一下滑鼠左鍵來執行該動作。

指令碼 **AcousticsAdjust** 會附加到範例場景中的聲音來源，其可啟用每個來源的設計參數。 

![AcousticsAdjust](media/acoustics-adjust.png)

以下會探索可透過所提供的控制項來產生的一些效果。 如需每個控制項的詳細資訊，請參閱[聲場專案 Unity 設計教學課程](unreal-workflow.md)。

### <a name="modify-distance-based-attenuation"></a>修改以距離為基礎的衰減
**聲場專案** Unity 空間定位外掛程式所提供的音訊 DSP，會遵循 Unity 編輯器所內建，以每個來源距離為基礎的衰減。 距離型衰減控制項位於聲音來源之 [Inspector] \(偵測器\) 面板中 [Audio Source] \(音訊來源\) 元件的 [3D Sound Settings] \(3D 聲音設定\) 底下：

![距離衰減](media/distance-attenuation.png)

聲場專案會在以玩家位置為中心的「模擬區域」方塊中執行計算。 由於範例套件中的聲場資產是以玩家周圍 45 公尺大小的模擬區域進行聲場模擬，因此聲音衰減應設計為在達到約 45 公尺的距離時便降至 0。

### <a name="modify-occlusion-and-transmission"></a>修改遮蔽和傳輸
* 如果 [Occlusion] \(遮蔽\) 的乘數大於 1 (預設值為 1)，遮蔽便會被放大。 將它設定為小於 1 會使遮蔽效果更細微。

* 若要啟用穿牆的傳輸，請將 [Transmission (dB)] \(傳輸 (dB)\) 滑桿從其最低位置移開。 

### <a name="modify-wetness-for-a-source"></a>修改來源的濕潤度
* 若要變更濕潤度隨距離變更的程度，請使用 [Perceptual Distance Warp] \(感知距離變形\)。 **聲場專案**會透過模擬計算整個空間的濕潤層級，此數值會因距離而緩和變化，並為玩家提供感知距離的線索。增加距離變形將會提升與距離相關的濕潤層級，以放大此效果。 變形值小於 1 會使以距離為基礎的殘響變化變得更加細微。 這個效果也可以透過調整 [Wetness (dB)] \(濕潤度 (dB)\) 來以更細微的調整幅度加以調整。

* 透過調整 [Decay Time Scale] \(衰減時間刻度\) 來調整整個空間的衰減時間。 如果特定來源-聆聽者位置組的模擬結果為 1.5 秒的衰減時間，且 [Decay Time Scale] \(衰減時間刻度\) 已設為 2，則套用至來源的衰減時間將會是 3 秒。

## <a name="next-steps"></a>後續步驟
* 參閱[以 Unity 為基礎的聲場專案設計控制項](unity-workflow.md)中所提供的完整詳細資料
* 深入探索[設計程序](design-process.md)背後的概念
* [建立 Azure 帳戶](create-azure-account.md)以探索聲場模擬前與聲場模擬的程序

