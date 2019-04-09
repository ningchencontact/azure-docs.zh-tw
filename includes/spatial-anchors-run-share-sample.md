---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631150"
---
## <a name="set-up-your-device"></a>設定您的裝置

在 Unity 中，開啟 `Unity` 資料夾中的專案。

![Unity 視窗](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>設定 Android 裝置

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>設定 iOS 裝置

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

在 [專案] 窗格中瀏覽至 `Assets/AzureSpatialAnchorsPlugin/Examples`，然後開啟 `AzureSpatialAnchorsLocalSharedDemo.unity` 場景檔案。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

在 [偵測器] 窗格中，輸入 `Sharing Anchors Service url` (來自您的 ASP.NET Web 應用程式 Azure 部署) 作為 `Base Sharing Url` 的值，並將 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

選取 [檔案] > [儲存] 以儲存場景。

## <a name="deploy-to-your-device"></a>部署至裝置

### <a name="deploy-to-android-device"></a>部署至 Android 裝置

登入 Android 裝置並使用 USB 纜線將它連接到電腦。

藉由選取 [檔案] > [組建設定] 來開啟 [組建設定]。

在 [組建中的場景] 底下，於 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 場景旁邊打勾，並清除其他所有場景的核取標記。

確定 [匯出專案] 沒有核取記號。 選取 [建置並執行]。 系統將會提示您儲存 `.apk` 檔案。 您可以將檔案命名為任何名稱。

依照應用程式中的指示操作。 您可以選取 [建立及共用錨點] 或 [尋找共用的錨點]。 第一個案例可讓您建立後續可在相同或不同裝置上找到的錨點。 第二個案例可讓您找出先前共用的錨點 (如果已在相同或不同的裝置上執行過該應用程式)。 挑選案例之後，應用程式就會提供進一步的指示來指引您要如何進行。 例如，其會要求您移動裝置來收集環境資訊。 之後，您會在世界中放置錨點、靜候其上傳等等。

### <a name="deploy-to-an-ios-device"></a>部署到 iOS 裝置

藉由選取 [檔案] > [組建設定] 來開啟 [組建設定]。

在 [組建中的場景] 底下，於 `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` 場景旁邊打勾，並清除其他所有場景的核取標記。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

依照應用程式中的指示操作。 您可以選取 [建立及共用錨點] 或 [尋找共用的錨點]。 第一個案例可讓您建立後續可在相同或不同裝置上找到的錨點。 第二個案例可讓您找出先前共用的錨點 (如果已在相同或不同的裝置上執行過該應用程式)。 挑選案例之後，應用程式就會提供進一步的指示來指引您要如何進行。 例如，其會要求您移動裝置來收集環境資訊。 之後，您會在世界中放置錨點、靜候其上傳等等。

在 Xcode 中，選取 [停止] 來停止應用程式。
