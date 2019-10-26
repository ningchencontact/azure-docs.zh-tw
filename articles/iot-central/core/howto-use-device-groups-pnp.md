---
title: 在您的 Azure IoT Central 應用程式中使用裝置群組 |Microsoft Docs
description: 身為操作員，如何在您的 Azure IoT Central 應用程式中使用裝置群組。
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 91aae53ae81d2bc7cfda54cefa786e4b1a1aab67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949660"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>在 Azure IoT Central 應用程式中使用裝置群組（預覽功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文說明如何以操作員的身分，在 Azure IoT Central 應用程式中使用裝置群組。

裝置群組是群組在一起的裝置清單，因為它們符合某些指定的準則。 裝置群組可透過將裝置分組為較小的邏輯群組，協助您大規模管理、視覺化和分析裝置。 例如，您可以建立裝置群組來列出西雅圖的所有空中空調裝置，讓技術人員能夠尋找其負責的裝置。 本文說明如何建立和設定裝置群組。

>  [!NOTE] 
> 針對 Azure IoT Edge 裝置，您必須選取 Azure IoT Edge 範本來建立裝置群組

## <a name="create-a-device-group"></a>建立裝置群組

若要建立裝置群組：

1. 選擇左窗格中的 [**裝置群組**]。

1. 選取 [ **+ 新增**]。

    ![新增裝置群組](media/howto-use-device-groups-pnp/image1.png)

1. 為您的裝置群組提供在整個應用程式中唯一的名稱。 您也可以新增描述。 裝置群組只能包含來自單一裝置範本的裝置。 選擇要用於此群組的裝置範本。

1. 藉由選取屬性、比較運算子和值，建立查詢以識別裝置群組的裝置。 您可以新增多個查詢，且符合**所有**條件的裝置會放在裝置群組中。 可存取應用程式的任何人都能存取您所建立的裝置群組，因此任何人都可以查看、修改或刪除裝置群組。

    ![裝置群組查詢](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > 裝置群組是動態查詢。 您每次檢視裝置清單時所看到的裝置可能會不同。 清單內容取決於哪些裝置目前符合查詢準則。

1. 選擇 [儲存]。

## <a name="analytics"></a>分析

[裝置群組] 中的 [分析] 與左窗格中的 [主要分析] 索引標籤相同。 您可以在關於[如何建立分析](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)的文章中深入了解分析。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中使用裝置群組，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [如何建立遙測規則](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
