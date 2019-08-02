---
title: 使用 DPS 來自動佈建 Windows 裝置 - Azure IoT Edge | Microsoft Docs
description: 在您的 Windows 機器上使用模擬裝置，以透過裝置佈建服務測試 Azure IoT Edge 的自動裝置佈建
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0236491f9ebc8e3ecf7df8b74db4fd5ff441c7f8
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677448"
---
# <a name="create-and-provision-a-simulated-tpm-edge-device-on-windows"></a>在 Windows 上建立及佈建模擬 TPM Edge 裝置

Azure IoT Edge 裝置可用[裝置佈建服務](../iot-dps/index.yml)來自動佈建，就像未啟用 Edge 的裝置一樣。 如果您不熟悉自動佈建程序，請先檢閱[自動佈建概念](../iot-dps/concepts-auto-provisioning.md)，再繼續作業。

本文將說明如何藉由下列步驟，在模擬 Edge 裝置上測試自動佈建：

* 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
* 在 Windows 機器上建立模擬裝置與保護硬體的模擬信任平台模組 (TPM)。
* 建立裝置的個別註冊。
* 安裝 IoT Edge 執行階段，並將裝置連線到 IoT 中樞。

## <a name="prerequisites"></a>必要條件

* Windows 開發機器。 本文使用 Windows 10。
* 使用中的 IoT 中樞。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍] 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="simulate-a-tpm-device"></a>模擬 TPM 裝置

在 Windows 開發機器上建立模擬 TPM 裝置。 擷取您裝置的**註冊識別碼**和**簽署金鑰**，並使用它們在 DPS 中建立個別註冊項目。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-monitor.md)。

選擇您想要用來建立模擬裝置的 SDK 語言並遵循步驟，直到您建立個別註冊。

當您建立個別註冊時, 請選取 [ **True** ] 以宣告 Windows 開發電腦上的模擬 TPM 裝置是**IoT Edge 裝置**。

模擬裝置和個別註冊指南：

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

建立個別註冊之後，儲存 [註冊識別碼] 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

完成上一節後，您應該會看到新裝置在 IoT 中樞內列為 IoT Edge 裝置。 現在，您必須在裝置上安裝 IoT Edge 執行階段。

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。  

請依照指示，在執行模擬 TPM (已在上一節中建立) 的裝置上安裝 IoT Edge 執行階段。 請務必將 IoT Edge 執行階段設定為自動佈建，而不是手動佈建。

在裝置上安裝 IoT Edge 之前，請先了解您的 DPS **識別碼範圍**和裝置的**註冊識別碼**。

[安裝並自動佈建 IoT Edge](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>確認安裝成功

如果執行階段順利啟動，您可以移至 IoT 中樞，並開始將 IoT Edge 模組部署到您的裝置。 請在您的裝置上使用下列命令，確認執行階段已成功安裝並啟動。  

檢查 IoT Edge 服務的狀態。

```powershell
Get-Service iotedge
```

檢查最後 5 分鐘的服務記錄。


```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出執行中的模組。

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 了解如何[使用 Azure 入口網站大規模部署和監視 IoT Edge 模組](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 執行相同作業
