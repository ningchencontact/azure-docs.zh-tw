---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 72ccad94301e053d8103ca949d41202e58d9f5bb
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011657"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

「裝置對應項」是存放裝置狀態資訊 (中繼資料、組態和條件) 的 JSON 文件。 IoT 中心为连接到它的每台设备保留一个设备孪生。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

使用裝置對應項可以：

* 從您的解決方案後端儲存裝置中繼資料。

* 通过设备应用报告当前状态信息，例如可用功能和条件（例如，使用的连接方法）。

* 同步處理裝置應用程式與後端應用程式之間長時間執行之工作流程的狀態 (例如韌體和組態更新)。

* 查询设备的元数据、配置或状态。

裝置對應項是設計來進行同步處理和查詢裝置組態和條件。 如需何時使用裝置對應項的詳細資訊，請參閱[了解裝置對應項](../articles/iot-hub/iot-hub-devguide-device-twins.md)。

裝置對應項會儲存在 IoT 中樞，並且包含︰

* 標籤，只有解決方案後端可存取裝置中繼資料；

* 所需屬性，JSON 物件可由解決方案後端修改並且可由裝置應用程式觀察，以及

* 報告屬性，JSON 物件可由裝置應用程式修改並且可由解決方案後端讀取。 標籤和屬性不能包含陣列，但物件可以是巢狀的。

![顯示功能的裝置對應項影像](./media/iot-hub-selector-twin-get-started/twin.png)

此外，解決方案後端可以根據上述的所有資料查詢裝置對應項。
請參閱[了解裝置對應項](../articles/iot-hub/iot-hub-devguide-device-twins.md)以取得裝置對應項的詳細資訊，以及參閱 [IoT 中樞查詢語言](../articles/iot-hub/iot-hub-devguide-query-language.md)參考以了解查詢。


本教學課程說明如何：

* 建立後端應用程式，將「標籤」新增至裝置對應項，以及建立模擬裝置應用程式，以裝置對應項上的「報告屬性」來報告其連線通道。

* 使用先前建立的標籤和屬性上的篩選器，從您的後端應用程式查詢裝置。
