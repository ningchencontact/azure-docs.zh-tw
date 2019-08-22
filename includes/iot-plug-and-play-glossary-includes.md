---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880458"
---
## <a name="iot-plug-and-play-device"></a>IoT 隨插即用裝置

IoT 隨插即用裝置通常是小型的獨立計算裝置, 可收集資料或控制其他裝置, 並執行軟體或固件, 以實作為[裝置功能模型](#device-capability-model)中所宣告的功能。  例如, IoT 隨插即用裝置可能是環境監視裝置, 或是智慧型 agrigulture 灌溉系統的控制器。 可以撰寫雲端裝載的 IoT 解決方案, 從 IoT 隨插即用裝置進行命令、控制和接收資料。 您可以透過[Azure IoT 認證裝置目錄](https://catalog.azureiotsolutions.com/)找到 IoT 隨插即用裝置。 目錄中的每個 IoT 隨插即用裝置都已經過驗證, 而且具有[裝置功能模型](#device-capability-model)。

## <a name="digital-twin"></a>數位對應項

數位 twins 是 IoT 隨插即用裝置的模型。  數位 twins 是使用數位對應項[定義語言](https://aka.ms/DTDL)來建立模型。  您可以使用 Azure IoT API 與數位 twins 進行互動。 

## <a name="digital-twin-definition-language"></a>數位對應項定義語言

用來描述[IoT 隨插即用裝置](#iot-plug-and-play-device)之模型和介面的語言。  使用數位對應項[定義語言](https://aka.ms/DTDL)來描述數位對應項[的](#digital-twin)功能, 並啟用 iot 平臺和 iot 解決方案以利用實體的語義。

## <a name="device-capability-model"></a>裝置功能模型

裝置功能模型會描述裝置, 並定義裝置所執行的介面集。 建立裝置功能模型, 以對應至實體裝置、產品或 SKU。

## <a name="interface"></a>介面

介面會描述裝置或數位對應項所執行的相關功能。 介面可以在不同的功能模型之間重複使用。

## <a name="property"></a>屬性

屬性是在[介面](#interface)中定義的資料欄位, 代表數位對應項的某些狀態。 您可以將屬性宣告為唯讀或可寫入。 唯讀屬性是由在 IoT 隨插即用裝置本身的內容中執行的程式碼 (例如序號) 所設定。  可寫入的屬性是由外部實體 (例如警示臨界值) 所設定。

## <a name="telemetry"></a>遙測

在[介面](#interface)中定義的遙測欄位代表測量。 這些測量通常是感應器讀數之類的值。

## <a name="command"></a>命令

在[介面](#interface)中定義的命令代表可以在數位對應項上執行的方法。 例如, 重設裝置的 reset 命令。
