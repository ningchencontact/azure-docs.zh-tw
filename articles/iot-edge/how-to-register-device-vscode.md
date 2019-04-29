---
title: 從 Visual Studio Code 註冊新的裝置 - Azure IoT Edge | Microsoft Docs
description: 使用 Visual Studio Code 在您的 Azure IoT 中樞中建立新的 IoT Edge 裝置並且擷取連接字串
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 01/03/2019
ms.date: 01/28/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2b851e7f2ebdbff08fa09002765fbd7d7927deba
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126285"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>從 Visual Studio Code 註冊新的 Azure IoT Edge 裝置

您必須先向 IoT 中樞註冊 IoT 裝置，才能搭配 Azure IoT Edge 使用 IoT 裝置。 裝置註冊好之後，您會收到一個連接字串，可以用來針對 Edge 工作負載來設定您的裝置。 

本文說明如何使用 Visual Studio Code (VS Code) 註冊新的 IoT Edge 裝置。 目前有多種方式可以在 VS Code 中執行大部分的操作。 本文使用總管，但您也可以使用命令選擇區來執行大部分的步驟。 

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

## <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

您可以使用適用於 Visual Studio Code 的 Azure IoT 擴充功能來透過 IoT 中樞執行各種操作。 為了讓這些作業發揮作用，您需要登入 Azure 帳戶，並選取現用的 IoT 中樞。

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。

2. 在總管底部，展開 [Azure IoT 中樞裝置] 區段。 

   ![展開 [Azure IoT 中樞裝置] 區段](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. 按一下 [Azure IoT 中樞裝置] 區段標題中的 **...**。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。 

4. 選擇 [選取 IoT 中樞]。

5. 如果您未登入 Azure 帳戶，請依照提示執行。 

6. 選取 Azure 訂用帳戶。 

7. 選取您的 IoT 中樞。 

## <a name="create-a-device"></a>建立一個裝置

1. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置] 區段。 

2. 按一下 [Azure IoT 中樞裝置] 區段標題中的 **...**。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。 

3. 選取 [建立 IoT Edge 裝置]。 

4. 在開啟的文字方塊中，提供裝置的識別碼。 

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。 

## <a name="view-all-devices"></a>檢視所有裝置

連線到 IoT 中樞的所有裝置都會列在 Visual Studio Code 總管的 [Azure IoT 中樞裝置] 區段中。 IoT Edge 裝置與非 Edge 裝置可透過不同的圖示來區別，另外前者可展開以顯示部署至每個裝置的模組。 

   ![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 以滑鼠右鍵按一下 [Azure IoT 中樞裝置] 區段中的裝置識別碼。 
2. 選取 [複製裝置連接字串]。

   連接字串會複製到剪貼簿。 

您也可以選取右鍵功能表的 [取得裝置資訊]，以在輸出視窗中查看所有裝置資訊，包括連接字串。 


## <a name="next-steps"></a>後續步驟

了解如何[使用 Visual Studio Code 將模組部署至裝置](how-to-deploy-modules-vscode.md)。
