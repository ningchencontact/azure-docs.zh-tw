---
title: 快速入門 - 使用 Azure Spatial Anchors 建立 Android 應用程式 | Microsoft Docs
description: 在本快速入門中，您將了解如何使用 Spatial Anchors 建置 Android 應用程式。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64e5e78f93488b2c375c617e8857c84ba2171f00
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822390"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>快速入門：使用 Azure Spatial Anchors 建立 Android 應用程式

本快速入門將說明如何透過 Java 或 C++/NDK 使用 [Azure Spatial Anchors](../overview.md) 建立 Android 應用程式。 Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有可儲存和回收空間錨點的 ARCore Android 應用程式。

您將學習如何：

> [!div class="checklist"]
> * 建立 Spatial Anchors 帳戶
> * 設定 Spatial Anchors 帳戶識別碼和帳戶金鑰
> * 在 Android 裝置上部署和執行

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，請確定您具備︰

- 具有 <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a> 的 Windows 或 macOS 機器。
  - 若要建置 NDK 範例，您也必須在 Android Studio 中安裝 NDK 和 CMake 3.6 SDK Tools。
- <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">由開發人員啟用</a>且<a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">具備 ARCore 功能</a>的 Android 裝置。
- 您的應用程式必須以 ARCore 1.5 為目標 (ARCore 1.6+ 即將推出)

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>開啟範例專案

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

如果您要建置 Android NDK 範例，則必須從[這裡](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.5.0/libraries/include/arcore_c_api.h)下載 `arcore_c_api.h`，並將其置於 `Android\NDK\libraries\include` 中。

開啟 Android Studio。

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

選取 [開啟現有的 Android Studio 專案]，並選取位於 `Android/Java/` 上的專案。

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

選取 [開啟現有的 Android Studio 專案]，並選取位於 `Android/NDK/` 上的專案。

***

## <a name="configure-account-identifier-and-key"></a>設定帳戶識別碼和金鑰

下一個步驟是使用先前設定 Spatial Anchors 資源時所記下的帳戶識別碼和帳戶金鑰，來設定應用程式。

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

開啟 `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`。

找出 `SpatialAnchorsAccountKey` 欄位，並將 `Set me` 取代為帳戶金鑰。

找出 `SpatialAnchorsAccountId` 欄位，並將 `Set me` 取代為帳戶識別碼。

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

開啟 `Android/NDK/app/src/main/cpp/spatial_services_application.cc`。

找出 `SpatialAnchorsAccountKey` 欄位，並將 `Set me` 取代為帳戶金鑰。

找出 `SpatialAnchorsAccountId` 欄位，並將 `Set me` 取代為帳戶識別碼。

***

## <a name="deploy-the-app-to-your-android-device"></a>將應用程式部署到您的 Android 裝置

將 Android 裝置開機並登入，然後使用 USB 纜線將其連接到電腦。

在 Android Studio 工具列中選取 [執行]。

![Android Studio 的部署和執行](./media/get-started-android/android-studio-deploy-run.png)

在 [選取部署目標] 對話方塊中選取 Android 裝置，然後選取 [確定] 以在 Android 裝置上執行應用程式。

依照應用程式中的指示放置及回收錨點。

在 Android Studio 工具列中選取 [停止]，以停止應用程式。

![Android Studio 的停止](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [教學課程：跨裝置共用 Spatial Anchors](../tutorials/tutorial-share-anchors-across-devices.md)
