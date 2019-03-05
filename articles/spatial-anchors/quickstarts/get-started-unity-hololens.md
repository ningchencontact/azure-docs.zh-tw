---
title: 快速入門 - 使用 Azure Spatial Anchors 建立 HoloLens Unity 應用程式 | Microsoft Docs
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置搭配 Unity 的 HoloLens 應用程式。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b2bfec47bc92ebf5db1561d8fca33940dc376866
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752611"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>快速入門：使用 Azure Spatial Anchors 建立 HoloLens Unity 應用程式

本快速入門將說明如何使用 [Azure Spatial Anchors](../overview.md) 建立 HoloLens Unity 應用程式。 Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有搭配 Unity 建置的 HoloLens 應用程式，並可儲存和回收空間錨點。

您將學習如何：

> [!div class="checklist"]
> * 建立 Spatial Anchors 帳戶
> * 準備 Unity 組建設定
> * 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰
> * 匯出 HoloLens Visual Studio 專案
> * 在 HoloLens 裝置上部署和執行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，請確定您具備︰

- 已安裝 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a> 和 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> 的 Windows 機器，並且具有**通用 Windows 平台開發**工作負載。
- 已啟用[開發人員模式](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio)的 HoloLens 裝置。
- 必須在 [組建設定]->[播放機設定]->[發佈設定]->[功能] 底下，為您的應用程式設定 [SpatialPerception] 功能。
- 必須在 [組建設定]->[播放機設定]->[XR 設定] 底下，以 **Windows Mixed Reality SDK** 為應用程式啟用 [支援虛擬實境]。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>在 Unity 中開啟範例專案

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

開啟 Unity 並在 `Unity` 資料夾上開啟專案。

藉由選取 [檔案] -> [組建設定] 來開啟 [組建設定]。

在 [平台] 區段中，選取 [通用 Windows 平台]。 然後，將 [目標裝置] 變更為 [HoloLens]。

選取 [切換平台]，將平台變更為 [通用 Windows 平台]。

![Unity 組建設定](./media/get-started-unity-hololens/unity-build-settings.png)

關閉 [組建設定] 視窗。

## <a name="configure-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

在 [專案] 窗格中瀏覽至 `Assets/AzureSpatialAnchorsPlugin/Examples`，然後開啟 `AzureSpatialAnchorsBasicDemo.unity` 場景檔案。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

選取 [檔案] -> [儲存] 以儲存場景。

## <a name="export-the-hololens-visual-studio-project"></a>匯出 HoloLens Visual Studio 專案

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

選取 [組建] 以開啟對話方塊。 然後選取資料夾，以匯出 HoloLens Visual Studio 專案。

匯出完成時，資料夾會顯示出已包含匯出的 HoloLens 專案。

## <a name="deploy-the-hololens-application"></a>部署 HoloLens 應用程式

在資料夾中按兩下 `HelloAR U3D.sln`，以在 Visual Studio 中開啟專案。

將 [方案設定] 變更為 [發行]，並將 [方案平台] 變更為 [x86]，然後從部署目標選項中選取 [裝置]。

![Visual Studio 設定](./media/get-started-unity-hololens/visual-studio-configuration.png)

將 HoloLens 裝置開機並登入，然後使用 USB 纜線將其連接到電腦。

選取 [偵錯] > [開始偵錯] 來部署您的應用程式並開始偵錯。

依照應用程式中的指示放置及回收錨點。

在 Visual Studio 中，您可以選取 [停止偵錯] 或按下 **Shift + F5** 來停止應用程式。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用 Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
