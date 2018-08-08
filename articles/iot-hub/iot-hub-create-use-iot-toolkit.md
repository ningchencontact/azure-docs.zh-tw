---
title: 使用適用於 VS Code 的 Azure IoT 工具組建立 Azure IoT 中樞 | Microsoft Docs
description: 如何使用適用於 VS Code 的 Azure IoT 工具組建立 IoT 中樞。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 71b188443ee48d742b14753cd5526edac493d9e3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368616"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>使用適用於 Visual Studio Code 的 Azure IoT 工具組建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

您可以使用[適用於 Visual Studio Code 的 Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)建立 Azure IoT 中樞。 本文說明如何使用 Azure CLI 工具組建立 IoT 中樞。

若要完成本文，您需要下列項目：

* 使用中的 Azure 帳戶。
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。

2. 在總管底部，展開 [Azure IoT 中樞裝置] 區段。 

   ![展開 Azure IoT 中樞裝置](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. 按一下 [Azure IoT 中樞裝置] 區段標題中的 **...**。 若未看到省略符號，請將滑鼠暫留在標題上方。 

4. 選擇 [建立 IoT 中樞]。

5. 快顯視窗會顯示在右下角，讓您第一次登入 Azure。

6. 選取 Azure 訂用帳戶。 

7. 選取資源群組。

8. 選取位置。

9. 選取定價層。

10. 輸入 IoT 中樞的全域唯一名稱。

11. 等候幾分鐘，直到 IoT 中樞建立。

## <a name="next-steps"></a>後續步驟

現在您已使用適用於 Visual Studio Code 的 Azure IoT 工具組建立 IoT 中樞，您可能想要進一步探索︰

* [使用適用於 Visual Studio Code 的 Azure IoT 工具組擴充功能來傳送和接收裝置與 IoT 中樞之間的訊息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。
* Azure IoT 工具組的 [Wiki 頁面](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)。
