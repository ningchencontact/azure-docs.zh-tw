---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903982"
---
## <a name="set-up-your-device-in-unity"></a>在 Unity 中設定您的裝置

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>設定 Android 裝置

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>設定 iOS 裝置

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

在 [專案]  窗格中瀏覽至 `Assets/AzureSpatialAnchorsPlugin/Examples`，然後開啟 `AzureSpatialAnchorsLocalSharedDemo.unity` 場景檔案。

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

在 [專案]  窗格中瀏覽至 `Assets\AzureSpatialAnchors.Examples\Resources`。 選取 `SpatialAnchorSamplesConfig`。 然後，在 [偵測器]  窗格中，輸入 `Sharing Anchors Service url` (來自您的 ASP.NET Web 應用程式 Azure 部署) 作為 `Base Sharing Url` 的值，並將 `index.html` 取代為 `api/anchors`。 它看起來應該像這樣：`https://<app_name>.azurewebsites.net/api/anchors`。

選取 [檔案]   > [儲存]  以儲存場景。

## <a name="deploy-to-your-device"></a>部署至裝置

### <a name="deploy-to-android-device"></a>部署至 Android 裝置

登入 Android 裝置並使用 USB 纜線將它連接到電腦。

藉由選取 [檔案]   > [組建設定]  來開啟 [組建設定]  。

在 [建置中的場景]  底下，確定所有場景旁邊都有勾號。

確定 [匯出專案]  沒有核取記號。 選取 [建置並執行]  。 系統將會提示您儲存 `.apk` 檔案。 您可以將檔案命名為任何名稱。

一旦應用程式啟動，在 [選擇示範]  對話方塊中，使用向左鍵或向右鍵來選取 [LocalShare]  選項，並且點選 [執行！]  。 依照應用程式中的指示操作。 您可以選取 [建立及共用錨點]  或 [尋找共用的錨點]  。

第一個案例可讓您建立後續可在相同或不同裝置上找到的錨點。
第二個案例可讓您找出先前共用的錨點 (如果已在相同或不同的裝置上執行過該應用程式)。 挑選案例之後，應用程式就會提供進一步的指示來指引您要如何進行。 例如，其會要求您移動裝置來收集環境資訊。 之後，您會在世界中放置錨點、靜候其儲存等等。

### <a name="deploy-to-an-ios-device"></a>部署到 iOS 裝置

藉由選取 [檔案]   > [組建設定]  來開啟 [組建設定]  。

在 [建置中的場景]  底下，確定所有場景旁邊都有勾號。

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

一旦應用程式啟動，在 [選擇示範]  對話方塊中，使用向左鍵或向右鍵來選取 [LocalShare]  選項，並且點選 [執行！]  。 依照應用程式中的指示操作。 您可以選取 [建立及共用錨點]  或 [尋找共用的錨點]  。

第一個案例可讓您建立後續可在相同或不同裝置上找到的錨點。
第二個案例可讓您找出先前共用的錨點 (如果已在相同或不同的裝置上執行過該應用程式)。 挑選案例之後，應用程式就會提供進一步的指示來指引您要如何進行。 例如，其會要求您移動裝置來收集環境資訊。 之後，您會在世界中放置錨點、靜候其儲存等等。

在 Xcode 中，選取 [停止]  來停止應用程式。
