---
title: 從 Visual Studio Code 註冊新的裝置 - Azure IoT Edge | Microsoft Docs
description: 使用 Visual Studio Code 在您的 Azure IoT 中樞中建立新的 IoT Edge 裝置並且擷取連接字串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c8fce104d48acc3a562599c65eb15cb0a66657b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495292"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>從 Visual Studio Code 註冊新的 Azure IoT Edge 裝置

您必須先向 IoT 中樞註冊 IoT 裝置，才能將 IoT 裝置與 Azure IoT Edge 搭配使用。 一旦您註冊裝置時，您會收到可用來設定您的裝置，IoT Edge 的工作負載的連接字串。

本文說明如何使用 Visual Studio Code (VS Code) 註冊新的 IoT Edge 裝置。 目前有多種方式可以在 VS Code 中執行大部分的操作。 這篇文章會使用 [總管] 中，但您也可以使用 「 命令選擇區 」 來執行的步驟。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可以使用適用於 Visual Studio Code 的 Azure IoT 擴充功能來透過 IoT 中樞執行各種操作。 這些作業運作，您需要登入您的 Azure 帳戶，並選取您的 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [總管]  檢視。

1. 在 [總管] 的底部，依序展開**Azure IoT 中樞**一節。

   ![展開 [Azure IoT 中樞裝置] 區段](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

1. 按一下 **...** 中**Azure IoT 中樞**區段標頭。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。

1. 選擇 [選取 IoT 中樞]  。

1. 如果您未登入您的 Azure 帳戶，請依照下列提示來執行這項操作。

1. 選取 Azure 訂用帳戶。

1. 選取您的 IoT 中樞。

## <a name="create-a-device"></a>建立一個裝置

1. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置]  區段。

1. 按一下 [Azure IoT 中樞裝置]  區段標題中的 **...** 。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。

1. 選取 [建立 IoT Edge 裝置]  。

1. 在開啟的文字方塊中，提供裝置的識別碼。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

## <a name="view-all-devices"></a>檢視所有裝置

連線至 IoT 中樞的所有裝置都會都列入**Azure IoT 中樞**Visual Studio Code 總管 」 一節。 IoT Edge 裝置會使用不同的圖示和事實的非邊緣裝置有所區別， **$edgeAgent**並 **$edgeHub**模組部署至每個 IoT Edge 裝置。

   ![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 在 裝置識別碼上按一下滑鼠右鍵**Azure IoT 中樞**一節。

1. 選取 [複製裝置連接字串]  。

   連接字串會複製到剪貼簿。

您也可以選取右鍵功能表的 [取得裝置資訊]  ，以在輸出視窗中查看所有裝置資訊，包括連接字串。

## <a name="next-steps"></a>後續步驟

了解如何[使用 Visual Studio Code 將模組部署至裝置](how-to-deploy-modules-vscode.md)。
