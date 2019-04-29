---
title: 遠端監視解決方案中的模擬裝置行為 - Azure | Microsoft Docs
description: 本文說明如何使用 JavaScript 定義模擬裝置在遠端監視解決方案中的行為。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c2151a4b1eb2a853ed343f6720b4f53af5e5e449
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446315"
---
# <a name="implement-the-device-model-behavior"></a>實作裝置模型行為

[了解裝置模型結構描述](iot-accelerators-remote-monitoring-device-schema.md)一文說明了定義模擬裝置模型的結構描述。 該文參考兩種類型的 JavaScript 檔案，這兩種類型都可以實作模擬裝置的行為：

- **State**：JavaScript 檔案，可在固定的間隔執行，以更新裝置的內部狀態。
- **Method**：JavaScript 檔案，可當方案在裝置上叫用方法時執行。

> [!NOTE]
> 裝置模型行為只適用於裝載於裝置模擬服務中的模擬裝置。 如果您想要建立真實裝置，請參閱[將裝置連線到遠端監視解決方案加速器](iot-accelerators-connecting-devices.md)。

在本文中，您將了解：

>[!div class="checklist"]
> * 控制模擬裝置的狀態
> * 定義模擬裝置從遠端監視解決方案回應方法呼叫的方式
> * 為指令碼偵錯

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>後續步驟

本文說明如何定義您自己的自訂模擬裝置模型行為。 本文示範如何：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 控制模擬裝置的狀態
> * 定義模擬裝置從遠端監視解決方案回應方法呼叫的方式
> * 為指令碼偵錯

現在您已經學會如何指定模擬裝置的行為，建議的下一個步驟是了解如何[建立模擬的裝置](iot-accelerators-remote-monitoring-create-simulated-device.md)。

如需關於遠端監視解決方案的開發人員詳細資訊，請參閱：

* [開發人員參考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開發人員疑難排解指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
