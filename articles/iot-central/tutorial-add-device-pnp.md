---
title: 將實際裝置新增至 Azure IoT Central 應用程式 | Microsoft Docs
description: 以操作員的身分，將實際裝置新增至 Azure IoT Central 應用程式。
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878859"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>教學課程：將模擬裝置新增至 Azure IoT Central 應用程式 (預覽功能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

本教學課程說明如何在 Microsoft Azure IoT Central 應用程式中新增和設定模擬裝置。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 新增模擬裝置
> * 在建置體驗中使用模擬裝置

## <a name="prerequisites"></a>必要條件

在開始之前，建置者應完成第一個建置者教學課程，以建立 Azure IoT Central 應用程式：

* [定義新的裝置類型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (必要)

## <a name="add-a-simulated-device"></a>新增模擬裝置

若要將實際裝置新增至應用程式，您應使用在[定義新的裝置類型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)教學課程中建立的**環境感應器**裝置範本。

1. 若要以操作員的身分新增裝置，請選擇左側導覽功能表中的 [裝置]  。 [裝置]  索引標籤會顯示 [所有裝置]  和 [環境感應器]  裝置範本。

1. 若要新增模擬的環境感應器裝置，請選取 [+ 新增]  。 請使用建議的**裝置識別碼**，或輸入您自己的小寫**裝置識別碼**。 您也可以輸入新裝置的名稱。 將 [模擬]  切換開關切換為 [開啟]  ，然後選取 [建立]  。

    ![模擬裝置](./media/tutorial-add-device-pnp/simulated-device.png)

現在，您可以與建置者使用模擬資料為裝置範本建立的檢視進行互動。

## <a name="use-a-simulated-device-to-improve-views"></a>使用模擬裝置來改善檢視

在您建立新的模擬裝置之後，建置者可以使用此裝置繼續改善及建置裝置範本的檢視。

1. 在裝置的檢視中，複製您所建立之模擬裝置的 [裝置識別碼]  。

1. 選擇左側導覽功能表中的 [裝置範本]  索引標籤，然後選取 [環境感應器]  範本。

1. 選取您要編輯的任何檢視，或建立新的檢視。 按一下 [設定預覽裝置]  。 在此，您可以選擇沒有預覽裝置、使用您可設定以進行測試的實際裝置，或您已新增至 IoT Central 的現有裝置。

1. 選擇 [從執行中的裝置選取]  ，然後輸入您已複製之模擬裝置的 [裝置識別碼]  。

1. 選擇 [套用]  。 現在，您可以在裝置範本檢視建置體驗中看到相同的模擬裝置。 此檢視特別適用於圖表和其他視覺效果。

    ![設定預覽裝置](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

* 新增模擬裝置
* 在建置體驗中使用模擬裝置

現在，您已將模擬裝置連線至 Azure IoT Central 應用程式，以下是一些建議的後續步驟。

身為操作員，您可以了解如何：

> [!div class="nextstepaction"]
> [設定規則](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

身為裝置開發人員，您可以了解如何：

> [!div class="nextstepaction"]
> [將 MXChip IoT DevKit 裝置連線到您的 Azure IoT Central 應用程式](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



