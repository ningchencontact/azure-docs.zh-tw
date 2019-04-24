---
title: 上傳影像至您的 Azure IoT Central 應用程式 | Microsoft Docs
description: 了解如何以建置者的身分準備及上傳影像至您的 Azure IoT Central 應用程式。
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c8444fd57fe939dfb212d88c22a071c13dac691e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519050"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>準備及上傳影像至您的 Azure IoT Central 應用程式

本文說明如何以建置者的身分，透過上傳自訂影像來自訂您的 Azure IoT Central 應用程式。 例如，您可以使用裝置的圖片來自訂裝置儀表板。

## <a name="before-you-begin"></a>開始之前

若要完成這篇文章中的步驟，您需要下列項目︰

1. Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
1. 適用於對影像檔案進行縮放及調整大小的工具。

## <a name="choose-where-to-use-custom-images"></a>選擇要使用自訂影像的位置

您可以將自訂影像新增至 Azure IoT Central 應用程式中的下列位置：

* **我的應用程式**頁面

    ![[應用程式管理員] 頁面上的影像](media/howto-prepare-images/applicationmanager.png)

* 應用程式儀表板

    ![應用程式儀表板上的映像](media/howto-prepare-images/homepage.png)

* 裝置範本

    ![裝置範本上的影像](media/howto-prepare-images/devicetemplate.png)

* 裝置儀表板上的磚

    ![裝置磚上的影像](media/howto-prepare-images/devicetile.png)

* 裝置集合儀表板上的磚

    ![裝置集合磚上的影像](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>準備影像

在上述四個位置中，您可以使用 PNG、GIF 或 JPEG 影像。

下表摘要說明您可以使用的影像大小：

| 位置 | 大小 |
| -------- | ------ |
| 應用程式管理員 | 268x160 px |
| 裝置範本 | 64x64 px |
| 儀表板磚 | 最小的磚大小為 200x200 px，較大的磚則可以是由多個小磚組合而成的正方形或矩形。 例如 200x400 px、400x200 px 或 400x400 px |

如需在應用程式中取得最佳顯示效果，您應該建立符合上表所述尺寸的影像。

## <a name="upload-the-images"></a>上傳影像

下列各節說明如何在不同位置上傳影像：

### <a name="application-manager"></a>應用程式管理員

若要使用的映像上傳**我的應用程式**頁面上，瀏覽至**應用程式設定**頁面**管理**區段。 您必須具有系統管理員的身分以完成此工作：

![上傳應用程式影像](media/howto-prepare-images/uploadapplicationmanager.png)

選取要上傳您已備妥的映像的 [應用程式映像] 圖格 (268 x 160 像素) 從本機電腦。

### <a name="application-dashboard"></a>應用程式儀表板

若要上傳應用程式儀表板上的影像，請瀏覽至**儀表板**頁面上，您的應用程式，然後選取**編輯**。 您必須具有建置者的身分以完成此工作：

![上傳儀表板影像](media/howto-prepare-images/uploadhomepage.png)

在設定的映像，選取要上傳您已備妥的映像，從本機電腦的 [映像] 圖格。 最小的磚大小為 200x200 px，較大的磚則可以是由多個小磚組合而成的正方形或矩形。 例如 200x400 px、400x200 px 或 400x400 px。

**儲存**已上傳的影像。 您可以在處於編輯模式時進行調整。 選取 **完成**時完成。

### <a name="device-template"></a>裝置範本

若要將影像上傳至裝置範本，請瀏覽至 [裝置範本]，然後選擇裝置範本。 您必須具有建置者的身分以完成此工作：

![上傳裝置範本影像](media/howto-prepare-images/uploaddevicetemplate.png)

選取要上傳您已備妥的映像的映像 圖格 (64 x 64 像素) 從本機電腦。

### <a name="device-dashboard"></a>裝置儀表板

若要將影像上傳至裝置儀表板，請瀏覽至 [裝置範本]，然後選擇裝置範本。 然後，選擇 [儀表板] 索引標籤。您必須具有建置者的身分以完成此工作：

![上傳裝置儀表板影像](media/howto-prepare-images/uploaddevicedashboard.png)

在設定的映像，選取 [映像] 圖格，，然後選擇要從本機電腦上傳的檔案。 最小的磚大小為 200x200 px，較大的磚則可以是由多個小磚組合而成的正方形或矩形。 例如 200x400 px、400x200 px 或 400x400 px。

**儲存**已上傳的影像。 您可以在處於編輯模式時進行調整並重新定位。 選取 **完成**時完成。

### <a name="device-set-dashboard"></a>裝置集合儀表板

若要在裝置集合儀表板上上傳影像，請瀏覽至 [裝置集合]，選擇裝置集合，然後選擇裝置。 然後選擇**儀表板**頁面，然後選取**編輯**:

![上傳裝置集合儀表板影像](media/howto-prepare-images/uploaddevicesetdashboard.png)

在設定的映像，選取要上傳您已備妥的映像，從本機電腦的 [映像] 圖格。 最小的磚大小為 200x200 px，較大的磚則可以是由多個小磚組合而成的正方形或矩形。 例如 200x400 px、400x200 px 或 400x400 px。

**儲存**已上傳的影像。 您可以在處於編輯模式時進行調整並重新定位。 選取 **完成**時完成。

## <a name="next-steps"></a>後續步驟

您現在已了解如何準備及上傳影像至 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [在 Azure IoT Central 應用程式中管理裝置](howto-manage-devices.md)