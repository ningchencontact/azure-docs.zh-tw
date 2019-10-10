---
title: 聲場專案 Unity 整合與部署
titlesuffix: Azure Cognitive Services
description: 本文說明如何將聲場專案 Unity 外掛程式整合到您的 Unity 專案。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243004"
---
# <a name="project-acoustics-unity-integration"></a>聲場專案 Unity 整合
本文說明如何將聲場專案 Unity 外掛程式整合到您的 Unity 專案。

軟體需求：
* 適用於 Windows 的 [Unity 2018.2+](https://unity3d.com) \(英文\)
* [聲場專案 Unity 封裝](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>匯入外掛程式
1. 將聲場 UnityPackage 匯入到您的專案中。 
 在 Unity 中，移至 [**資產**]  >  [匯**入套件**]  >  個**自訂套件**。

    ![Unity 匯入封裝功能表](media/import-package.png)  

1. 選擇 [ **ProjectAcoustics unitypackage**]。

1. 選取 [匯**入**] 按鈕，將 Unity 套件整合到您的專案中。

    ![[Unity 匯入封裝] 對話方塊](media/import-dialog.png)  

如果您要將外掛程式匯入現有的專案中，您的專案在專案根目錄中可能已經有一個*mcs .rsp*檔案。 這個檔案會指定編譯器的C#選項。 合併該檔案的內容與隨附于聲場專案外掛程式的 mcs .rsp 檔案。

## <a name="enable-the-plug-in"></a>啟用外掛程式
聲場工具組的製作部分需要 .NET 4。*x*腳本執行階段版本。 套件匯入會更新您的 Unity 播放機設定。 請重新啟動 Unity 來讓此設定生效。

![Unity 播放機設定面板](media/player-settings.png)

![已選取 [.NET 4.5] 的 Unity 播放機設定面板](media/net45.png)

## <a name="set-up-audio-dsp"></a>設定音訊 DSP
聲場專案包含整合到 Unity 音訊引擎空間定位器架構的音訊執行時間 DSP。 其中包含 HRTF 型和移動流覽 spatialization。 若要啟用聲場專案 DSP，請移至 [**編輯**] [ > ] [**專案設定**] [ > **音訊**] 以開啟 Unity 音訊設定。 選取 [**聲場專案**] 做為專案的**空間定位器外掛程式**。 請確定**DSP 緩衝區大小**設定為 [*最佳效能*]。

![Unity 專案設定功能表](media/project-settings.png)  

![已選取聲場專案空間定位器的 Unity 空間定位器設定面板](media/choose-spatializer.png)

接下來，開啟 [音訊混音器 **] （視窗** >  個**音訊混音**器）。 請確定您至少有一個混音器，其中包含一個群組。 如果您沒有，請選取 [ **Mixers**] 右邊的 [ **+** ] 按鈕。 在效果區段中聲道控制排的底部上按一下滑鼠右鍵，然後新增 [Microsoft Acoustics Mixer] \(Microsoft Acoustics 混音器\) 效果。 一次只支援一個聲場專案混音器。

![裝載聲場專案混音器的 Unity 音訊混音器](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>啟用音效來源的聲場
建立音訊來源：選取 [Spatialize 偵測器] 面板底部的 [ **Spatialize** ] 核取方塊。 請確定 [**空間 Blend** ] 已設定為 [完整*3d*]。  

![Unity 音訊來源面板](media/audio-source.png)

## <a name="enable-acoustic-design"></a>啟用聲場設計
將*AcousticsAdjust*腳本附加至場景中的音效來源，以啟用其他來源設計參數：選取 [**新增元件**]，然後選擇 [**腳本**] [ >  聲場**調整**]。

![Unity AcousticsAdjust 腳本](media/acoustics-adjust.png)

## <a name="next-steps"></a>後續步驟
* [使用適用于 Unity 的聲場專案來製作場景](unity-baking.md)。
* [建立 Azure Batch 帳戶](create-azure-account.md)，以在雲端中製作您的場景。
* 探索[聲場專案 Unity 設計流程](unity-workflow.md)。
