---
title: 快速入門 - 使用 Azure Spatial Anchors 建立 iOS Unity 應用程式 | Microsoft Docs
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置搭配 Unity 的 iOS 應用程式。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: e3320cd6131497d0b2c794646bae7fae578488cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995570"
---
# <a name="quickstart-create-an-ios-unity-app-with-azure-spatial-anchors"></a>快速入門：使用 Azure Spatial Anchors 建立 iOS Unity 應用程式

本快速入門說明如何使用 [Azure Spatial Anchors](../overview.md) 建立 iOS Unity 應用程式。 Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有搭配 Unity 建置的 ARKit iOS 應用程式，可用來儲存和回收空間錨點。

您將學習如何：

> [!div class="checklist"]
> * 建立 Spatial Anchors 帳戶
> * 準備 Unity 組建設定
> * 下載並匯入 Unity ARKit 外掛程式
> * 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰
> * 匯出 Xcode 專案
> * 在 iOS 裝置上部署和執行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，請確定您具備︰

- 已安裝 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3+</a>、<a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> 和 <a href="https://cocoapods.org" target="_blank">CocoaPods</a> 的 macOS 機器。
- 透過 HomeBrew 安裝的 Git。 在終端機的單一行輸入以下命令：`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`。 然後執行 `brew install git`。
- 由開發人員啟用且<a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">與 ARKit 相容</a>的 iOS 裝置。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>在 Unity 中開啟範例專案

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

在 [專案] 窗格中瀏覽至 `Assets/AzureSpatialAnchorsPlugin/Examples`，然後開啟 `AzureSpatialAnchorsBasicDemo.unity` 場景檔案。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

選取 [檔案] -> [儲存] 以儲存場景。

## <a name="export-the-xcode-project"></a>匯出 Xcode 專案

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

依照應用程式中的指示放置及回收錨點。

> [!NOTE]
> 在執行應用程式時，如果您看到的背景不是相機 (例如，您看到空白、藍色或其他紋理的背景)，則可能需要在 Unity 中重新匯入資產。 請停止應用程式。 在 Unity 的頂端功能表中，選擇 [資產] -> [全部重新匯入]。 然後，再次執行應用程式。

在 Xcode 中，按下 [停止] 即可停止應用程式。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用 Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
