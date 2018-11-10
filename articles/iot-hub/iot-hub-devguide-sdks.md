---
title: 了解 Azure IoT SDK | Microsoft Docs
description: 開發人員指南 - 可用來建置裝置應用程式和後端應用程式的各種 Azure IoT 裝置和服務 SDK 的相關資訊和連結。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: 9daca1876a291cb1f726e7c0eb4840c57f5c54c8
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741312"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>了解和使用 Azure IoT 中樞 SDK

有三種類別的軟體開發套件 (SDK) 可與「IoT 中樞」搭配使用：

* **裝置 SDK** 可讓您使用裝置用戶端或模組用戶端，來建置要在 IoT 裝置上執行的應用程式。 這些應用程式會將遙測傳送至您的 IoT 中樞，並選擇性地接收來自 IoT 中樞的訊息、作業、方法或對應項更新。  您也可以使用模組用戶端來撰寫適用於 [Azure IoT Edge 執行階段](../iot-edge/about-iot-edge.md)的[模組](../iot-edge/iot-edge-modules.md)。

* **服務 SDK** 可讓您管理 IoT 中樞，並選擇性地傳送訊息、排程作業、叫用直接方法，或是將所需的屬性更新傳送到您的 IoT 裝置或模組。

* **裝置佈建 SDK** 可讓您使用[裝置佈建服務](../iot-dps/about-iot-dps.md)，將裝置佈建到您的 IoT 中樞。

了解[使用 Azure IoT SDK 進行開發的優點](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) \(英文\)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT 裝置 SDK

Microsoft Azure IoT 裝置 SDK 包含有助於建置裝置和應用程式的程式碼，並使裝置和應用程式連接到 Azure IoT 中心服務，且由其進行管理。

適用於 .NET 的 Azure IoT 中樞裝置 SDK： 

* 從 [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) \(英文\) 安裝
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-csharp) \(英文\)
* [API 參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [模組參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) \(英文\)

適用於 C 的 Azure IoT 中樞裝置 SDK，基於可攜性和廣泛平台相容性而以 ANSI C (C99) 撰寫：

* 從 [apt-get、MBED、Arduino IDE 或 Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md) \(英文\) 安裝
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-c) \(英文\)
* [API 參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [模組參考](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h) \(英文\)

適用於 Java 的 Azure IoT 中樞裝置 SDK： 

* 新增至 [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)\(英文\) 專案
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-java) \(英文\)
* [API 參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [模組參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable) \(英文\)

適用於 Node.js 的 Azure IoT 中樞裝置 SDK： 

* 從 [npm](https://www.npmjs.com/package/azure-iot-device) \(英文\) 安裝
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-node) \(英文\)
* [API 參考](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [模組參考](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest) \(英文\)

適用於 Python 的 Azure IoT 中樞裝置 SDK： 

* 從 [pip](https://pypi.python.org/pypi/azure-iothub-device-client/) \(英文\) 安裝
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-python) \(英文\)
* API 參考：請參閱 [C API 參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) \(英文\)

適用於 iOS 的 Azure IoT 中樞裝置 SDK： 

* 從 [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) \(英文\) 安裝
* [範例](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API 參考：請參閱 [C API 參考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) \(英文\)

> [!NOTE]
> 請參閱 GitHub 儲存機制中的讀我檔案，以取得使用語言和平台特定套件管理員在開發電腦上安裝二進位檔和相依項目的相關資訊。
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>作業系統平台和硬體相容性

您可以在 [Azure IoT SDK 平台支援](iot-hub-device-sdk-platform-support.md)中找到支援 SDK 的平台。

如需 SDK 與特定硬體裝置之相容性的詳細資訊，請參閱 [Azure IoT 認證裝置目錄](https://catalog.azureiotsuite.com/) \(英文\) 或個別的存放庫。

## <a name="azure-iot-service-sdks"></a>Azure IoT 服務 SDK

Microsoft Azure IoT 服務 SDK 包含可協助建置應用程式的程式碼，這些應用程式可直接與「IoT 中樞」互動來管理裝置和安全性。

適用於 .NET 的 Azure IoT 中樞服務 SDK：

* 從 [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/) \(英文\) 下載
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-csharp) \(英文\)
* [API 參考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

適用於 Java 的 Azure IoT 中樞服務 SDK： 

* 新增至 [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
)\(英文\) 專案
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-java) \(英文\)
* [API 參考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

適用於 Node.js 的 Azure IoT 中樞服務 SDK： 

* 從 [npm](https://www.npmjs.com/package/azure-iothub) \(英文\) 下載
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-node) \(英文\)
* [API 參考](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

適用於 Python 的 Azure IoT 中樞服務 SDK： 

* 從 [pip](https://pypi.python.org/pypi/azure-iothub-service-client/) \(英文\) 下載
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-python) \(英文\)

適用於 C 的 Azure IoT 中樞服務 SDK： 

* 從 [apt-get、MBED、Arduino IDE 或 Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md) \(英文\) 下載
* [原始程式碼](https://github.com/Azure/azure-iot-sdk-c) \(英文\)

適用於 iOS 的 Azure IoT 中樞服務 SDK： 

* 從 [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient) \(英文\) 安裝
* [範例](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> 請參閱 GitHub 儲存機制中的讀我檔案，以取得使用語言和平台特定套件管理員在開發電腦上安裝二進位檔和相依項目的相關資訊。

## <a name="device-provisioning-sdks"></a>裝置佈建 SDK

**Microsoft Azure 佈建 SDK** 可讓您使用[裝置佈建服務](../iot-dps/about-iot-dps.md)，將裝置佈建到您的 IoT 中樞。

適用於 C# 的 Azure 佈建裝置與服務 SDK：

* [佈建裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [佈建服務用戶端 SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

適用於 Java 的 Azure 佈建裝置與服務 SDK：

* [佈建裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [佈建服務用戶端 SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

適用於 Node.js 的 Azure 佈建裝置與服務 SDK：

* [佈建裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [佈建服務用戶端 SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

適用於 Python 的 Azure 佈建裝置與服務 SDK：

* [佈建裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [佈建服務用戶端 SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

適用於 C 的 Azure 佈建裝置與服務 SDK：

* [佈建裝置用戶端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [佈建服務用戶端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>後續步驟

Azure IoT SDK 也提供一組工具以協助開發：
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics)：跨平台命令列工具，可協助診斷與 IoT 中樞連線相關的問題。
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)：Windows 桌面應用程式，可連線至 IoT 中樞。

此 IoT 中樞開發人員指南中的其他參考主題包括︰

* [IoT 中樞端點](iot-hub-devguide-endpoints.md)
* [裝置對應項、作業和訊息路由的 IoT 中樞查詢語言](iot-hub-devguide-query-language.md)
* [配額和節流](iot-hub-devguide-quotas-throttling.md)
* [IoT 中樞的 MQTT 支援](iot-hub-mqtt-support.md)
* [IoT 中樞 REST API 參考](/rest/api/iothub/)
* [Auzre IoT SDK 平台支援](iot-hub-device-sdk-platform-support.md)
