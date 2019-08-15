---
title: 聲場專案 Unity 整合與部署
titlesuffix: Azure Cognitive Services
description: 本 how to 說明如何將聲場專案 Unity 外掛程式整合到您的 Unity 專案。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 54bc98e0ddba0292c6a5dbb07f2bbdfce6a1cb45
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933151"
---
# <a name="project-acoustics-unity-integration"></a>聲場專案 Unity 整合
本 how to 說明如何將聲場專案 Unity 外掛程式整合到您的 Unity 專案。

軟體需求：
* 適用於 Windows 的 [Unity 2018.2+](https://unity3d.com) \(英文\)
* [聲場專案 Unity 封裝](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>匯入外掛程式
將聲場 UnityPackage 匯入到您的專案中。 
* 在 Unity 中，移至 [Assets] \(資產\) > [Import Package] \(匯入套件\) > [Custom Package] \(自訂套件\)

    ![Unity 匯入封裝功能表的螢幕擷取畫面](media/import-package.png)  

* 選擇 **ProjectAcoustics.unitypackage**

* 按一下 [匯**入**] 按鈕, 將 Unity 套件整合到您的專案中  

    ![[Unity 匯入封裝] 對話方塊的螢幕擷取畫面](media/import-dialog.png)  

如果您要將外掛程式匯入到現有的專案中，您的專案可能在專案根目錄中已經有 **mcs.rsp** 檔案，該檔案會指定 C# 編譯器的選項。 您將需要把該檔案的內容與 Project Acoustics 外掛程式隨附的 mcs.rsp 檔案合併。

## <a name="enable-the-plugin"></a>啟用外掛程式
聲場工具組的製作部分需要 .NET 4.x 指令碼處理執行階段版本。 套件匯入將會更新您的 Unity 播放器設定。 請重新啟動 Unity 來讓此設定生效。

![Unity 播放器設定面板的螢幕擷取畫面](media/player-settings.png)

![Unity 播放器設定面板 (已選取 .NET 4.5) 的螢幕擷取畫面](media/net45.png)

## <a name="set-up-audio-dsp"></a>設定音訊 DSP
聲場專案包含整合到 Unity 音訊引擎空間定位器架構的音訊執行時間 DSP。 其中包含 HRTF 型和移動流覽 spatialization。 使用 [**編輯 > 專案設定] > [音訊**] 開啟 Unity 音訊設定, 然後選取 [**聲場專案**] 做為專案的**空間定位器外掛程式**, 以啟用聲場專案 DSP。 請確定**DSP 緩衝區大小**設定為 [最佳效能]。

![Unity 專案設定面板的螢幕擷取畫面](media/project-settings.png)  

![已選取聲場專案空間定位器之 Unity 空間定位器設定面板的螢幕擷取畫面](media/choose-spatializer.png)

然後開啟音訊混音器 (**視窗 > 音訊混音**器)。 請確定您至少有一個含有一個群組的「混音器」。 如果沒有，請按一下 [Mixers] \(混音器\) 右邊的 [+] 按鈕。 以滑鼠右鍵按一下 [效果] 區段中頻道區域的底部, 然後新增**聲場專案混音**器效果。 請注意，一次只支援一個「Project Acoustics 混音器」。

![Unity 音訊混音器主控聲場專案混音器的螢幕擷取畫面](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>啟用音效來源的聲場
建立音訊來源。 按一下 AudioSource 偵測器面板底部的 [Spatialize] \(空間定位\) 核取方塊。 請確定 [Spatial Blend] \(空間混合\) 已設定為完全 3D。  

![Unity 音訊來源面板的螢幕擷取畫面](media/audio-source.png)

## <a name="enable-acoustic-design"></a>啟用聲場設計
將腳本**AcousticsAdjust**附加至場景中的聲音來源, 以啟用其他來源設計參數, 方法是按一下 [**新增元件**], 然後選擇 [**腳本] >** 聲場調整:

![Unity AcousticsAdjust 指令碼的螢幕擷取畫面](media/acoustics-adjust.png)

## <a name="next-steps"></a>後續步驟
* [使用適用于 Unity 的聲場專案製作您的場景](unity-baking.md)
* [建立 Azure Batch 帳戶](create-azure-account.md)以在雲端中製作您的場景
* 探索[聲場專案 Unity 設計流程](unity-workflow.md)。

