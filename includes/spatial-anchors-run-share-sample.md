---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753446"
---
## <a name="open-the-sample-project-in-unity"></a>在 Unity 中開啟範例專案

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>進行 Android 裝置的設定

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>進行 iOS 裝置的設定

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

在 [專案] 窗格中瀏覽至 `Assets/AzureSpatialAnchorsPlugin/Examples`，然後開啟 `AzureSpatialAnchorsLocalSharedDemo.unity` 場景檔案。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

此外，在 [偵測器] 窗格中，輸入 `Sharing Anchors Service url` (來自您的 ASP.NET Web 應用程式 Azure 部署) 作為 `Base Sharing Url` 的值，並將 `index.html` 取代為 `api/anchors`。 因此，該 URL 應會顯示如下：`https://<app_name>.azurewebsites.net/api/anchors`。

選取 [檔案] -> [儲存] 以儲存場景。

## <a name="to-deploy-to-an-android-device"></a>部署至 Android 裝置

將 Android 裝置開機並登入，然後使用 USB 纜線將其連接到電腦。

藉由選取 [檔案] -> [組建設定] 來開啟 [組建設定]。

在 [組建中的場景] 底下，在 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 場景旁邊打勾，並清除其他所有場景的核取標記。

請確定 [匯出專案] 核取方塊並未勾選。 按一下 [建置並執行]。 系統會要求您儲存 `.apk` 檔案，您可以選擇任何名稱。

依照應用程式中的指示操作。 您可以選擇 [建立及共用錨點] 或 [尋找共用的錨點]。 第一個選項可讓您建立後續可在相同或不同裝置上找到的錨點。 第二個選項 (如果您先前已在相同或不同的裝置上執行應用程式) 可讓您找出先前共用的錨點。

## <a name="to-deploy-to-an-ios-device"></a>部署到 iOS 裝置

藉由選取 [檔案] -> [組建設定] 來開啟 [組建設定]。

在 [組建中的場景] 底下，在 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 場景旁邊打勾，並清除其他所有場景的核取標記。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

依照應用程式中的指示操作。 您可以選擇 [建立及共用錨點] 或 [尋找共用的錨點]。 第一個選項可讓您建立後續可在相同或不同裝置上找到的錨點。 第二個選項 (如果您先前已在相同或不同的裝置上執行應用程式) 可讓您找出先前共用的錨點。

在 Xcode 中，按下 [停止] 即可停止應用程式。
