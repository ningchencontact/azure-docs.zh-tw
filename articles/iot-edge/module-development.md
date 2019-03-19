---
title: 開發適用於 Azure IoT Edge 的模組 | Microsoft Docs
description: 開發可與執行階段和 IoT 中樞通訊的 Azure IoT Edge 自訂模組
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ea2d1dbcf991ce858f07905bc1bb41809f9bcdd8
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882734"
---
# <a name="develop-your-own-iot-edge-modules"></a>開發您自己的 IoT Edge 模組

Azure IoT Edge 模組可以與其他 Azure 服務連線，並提供給您更大的雲端資料管線。 本文說明如何開發模組以便與 IoT Edge 執行階段和 IoT 中樞進行通訊，從而開發 Azure 雲端的其餘部分。 

## <a name="iot-edge-runtime-environment"></a>IoT Edge 執行階段環境
IoT Edge 執行階段提供基礎結構來整合多個 IoT Edge 模組的功能，並將其部署至 IoT Edge 裝置。 概括而言，任何程式都可封裝為 IoT Edge 模組。 不過，若要完全利用 IoT Edge 通訊和管理功能，在模組中執行的程式可以連線到本機 IoT Edge 中樞 (已在 IoT Edge 執行階段整合)。

## <a name="using-the-iot-edge-hub"></a>使用 IoT Edge 中樞
IoT Edge 中樞提供兩個主要功能：Proxy 到 IoT 中樞，以及本機通訊。

### <a name="iot-hub-primitives"></a>IoT 中樞基本項目
IoT 中樞會看見一個類似裝置的模組執行個體，這表示：

* 它具有一個模組對應項，其與[裝置對應項](../iot-hub/iot-hub-devguide-device-twins.md)及該裝置的其他模組對應項截然不同；
* 它會傳送[裝置到雲端的訊息](../iot-hub/iot-hub-devguide-messaging.md)；
* 它可以接收特別在其身分識別上設為目標的[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

模組目前無法接收雲端到裝置的訊息，也無法使用檔案上傳功能。

撰寫模組時，您可以使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md) 來連線到 IoT Edge 中樞，並使用上述功能，就像您在搭配裝置應用程式使用 IoT 中樞時所做的，唯一差異在於您必須從應用程式後端參考模組識別，而不是參考裝置識別。

### <a name="device-to-cloud-messages"></a>裝置到雲端的訊息
為了能夠對裝置到雲端的訊息進行複雜處理，IoT Edge 中樞在模組之間以及模組與 IoT 中樞之間提供宣告式路由傳送。 宣告式路由可讓模組攔截並處理其他模組所傳送的訊息，並將它們傳播至複雜的管線。 如需詳細資訊，請參閱[在 IoT Edge 中部署模組及建立路由](module-composition.md)。

IoT Edge 模組 (相對於一般 IoT 中樞裝置應用程式) 可接收其本機 IoT Edge 中樞正在進行 Proxy 處理的裝置到雲端訊息，以處理它們。

IoT Edge 中樞會根據[模組資訊清單](module-composition.md)中所述的宣告式路由，將訊息傳播至模組。 在開發 IoT Edge 模組時，您可以透過設定訊息處理常式來接收這些訊息。

為了簡化路由的建立，IoT Edge 新增了模組*輸入*和*輸出*端點的概念。 模組可以接收路由傳送給它的所有裝置到雲端訊息，而不需指定任何輸入，而且可以在未指定任何輸出的情況下傳送裝置到雲端訊息。 不過，使用明確的輸入和輸出，可讓路由傳送規則更容易了解。 

最後，使用下列系統屬性來為 Edge 中樞所處理的裝置到雲端訊息加上戳記：

| 屬性 | 描述 |
| -------- | ----------- |
| $connectionDeviceId | 傳送訊息之用戶端的裝置識別碼 |
| $connectionModuleId | 傳送訊息之模組的模組識別碼 |
| $inputName | 收到這則訊息的輸入。 可以是空的。 |
| $outputName | 用來傳送訊息的輸出。 可以是空的。 |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>從模組連線到 IoT Edge 中樞
從模組連線到本機 IoT Edge 中樞涉及兩個步驟： 
1. 在您的應用程式中建立 ModuleClient 執行個體。
2. 請確定您的應用程式接受 IoT Edge 中樞在該裝置上所提供的憑證。

建立 ModuleClient 執行個體，以便將您的模組連接到裝置上執行的 IoT Edge 中樞，這與 DeviceClient 執行個體將 IoT 裝置連接到 IoT 中樞的方式類似。 如需 ModuleClient 類別及其通訊方法的詳細資訊，請參閱您慣用 SDK 語言的 API 參考：[C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)、[C 和 Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) 或 [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)。


## <a name="next-steps"></a>後續步驟

[準備 IoT Edge 開發和測試環境](development-environment.md)

[使用 Visual Studio 2017 來開發C#適用於 IoT Edge 模組](how-to-visual-studio-develop-csharp-module.md)

[使用 Visual Studio Code 開發 IoT Edge 模組](how-to-vs-code-develop-module.md)

