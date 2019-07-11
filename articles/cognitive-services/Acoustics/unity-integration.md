---
title: 樂器 Unity 整合和部署專案
titlesuffix: Azure Cognitive Services
description: 此 「 作法 」 說明專案樂器 Unity 外掛程式整合到您的 Unity 專案。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 2deddfd4e6c03b53306d8fbab3340dce464158b0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612729"
---
# <a name="project-acoustics-unity-integration"></a>Project 樂器 Unity 整合
此 「 作法 」 說明專案樂器 Unity 外掛程式整合到您的 Unity 專案。

軟體需求：
* 適用於 Windows 的 [Unity 2018.2+](https://unity3d.com) \(英文\)
* [專案樂器 Unity 封裝](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>匯入外掛程式
將聲場 UnityPackage 匯入到您的專案中。 
* 在 Unity 中，移至 [Assets] \(資產\) > [Import Package] \(匯入套件\) > [Custom Package] \(自訂套件\) 

    ![Unity 匯入封裝的螢幕擷取畫面 功能表](media/import-package.png)  

* 選擇 **ProjectAcoustics.unitypackage**

如果您要將外掛程式匯入到現有的專案中，您的專案可能在專案根目錄中已經有 **mcs.rsp** 檔案，該檔案會指定 C# 編譯器的選項。 您將需要把該檔案的內容與 Project Acoustics 外掛程式隨附的 mcs.rsp 檔案合併。

## <a name="enable-the-plugin"></a>啟用外掛程式
聲場工具組的製作部分需要 .NET 4.x 指令碼處理執行階段版本。 套件匯入將會更新您的 Unity 播放器設定。 請重新啟動 Unity 來讓此設定生效。

![Unity 播放器設定面板的螢幕擷取畫面](media/player-settings.png)

![Unity 播放器設定面板 (已選取 .NET 4.5) 的螢幕擷取畫面](media/net45.png)

## <a name="set-up-audio-dsp"></a>設定 音訊 DSP
專案樂器包含音訊的執行階段會整合成 Unity 音訊引擎空間架構的 DSP。 它包含 HRTF 式和移動的空間。 開啟使用 Unity 音訊設定，以啟用專案樂器 DSP**編輯 > 專案設定 > 音訊**，然後選取**專案樂器**做為**空間外掛程式**為您的專案。 請確定**DSP 緩衝區大小**設為達到最佳效能。

![Unity 專案設定的螢幕擷取畫面 面板](media/project-settings.png)  

![Unity 空間的螢幕擷取畫面設定 面板中選取的專案樂器空間與](media/choose-spatializer.png)

然後開啟 音訊 Mixer (**視窗中 > 音訊 Mixer**)。 請確定您至少有一個含有一個群組的「混音器」。 如果沒有，請按一下 [Mixers] \(混音器\)  右邊的 [+] 按鈕。 以滑鼠右鍵按一下 [效果] 區段中，在通道區域底部並新增**專案樂器 Mixer**效果。 請注意，一次只支援一個「Project Acoustics 混音器」。

![螢幕擷取畫面的 Unity 音訊 Mixer 裝載專案樂器混音器](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>啟用樂器音效來源
建立音訊來源。 按一下 AudioSource 偵測器面板底部的 [Spatialize] \(空間定位\)  核取方塊。 請確定 [Spatial Blend] \(空間混合\)  已設定為完全 3D。  

![Unity 音訊來源螢幕擷取畫面 面板](media/audio-source.png)

## <a name="enable-acoustic-design"></a>更柔和式設計
將指令碼連接**AcousticsAdjust**若要啟用其他來源的設計參數，依序按一下場景中的音效來源**新增元件**，然後選擇**指令碼 > 樂器調整**:

![Unity AcousticsAdjust 指令碼的螢幕擷取畫面](media/acoustics-adjust.png)

## <a name="next-steps"></a>後續步驟
* [加強您的 Unity 專案樂器場景](unity-baking.md)
* [建立 Azure Batch 帳戶](create-azure-account.md)來加強您在雲端中的場景
* 瀏覽[專案樂器 Unity 設計程序](unity-workflow.md)。

