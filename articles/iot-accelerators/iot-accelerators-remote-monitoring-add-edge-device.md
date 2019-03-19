---
title: 遠端監視解決方案新增 Edge 裝置 - Azure | Microsoft Docs
description: 本文說明如何將 IoT Edge 裝置新增至遠端監視解決方案加速器
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: d34ac159a216c5c77214b4c8b799a233c3671235
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749540"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>將 IoT Edge 裝置新增至遠端監視解決方案加速器

若要將 [IoT Edge](../iot-edge/about-iot-edge.md) 裝置新增至解決方案加速器，請完成下列兩個步驟：

1. 新增 Edge 裝置上**Device Explorer**在遠端監視解決方案加速器 web UI 中的頁面。
1. 在 Edge 裝置上安裝 IoT Edge 執行階段。

## <a name="add-the-iot-edge-device"></a>新增 IoT Edge 裝置

若要將 IoT Edge 裝置新增至遠端監視解決方案加速器，請瀏覽至 Web UI 中的 [Device Explorer] 頁面，然後按一下 [+ 新增裝置]。

在 [新增裝置] 面板中，選擇 [IoT Edge 裝置]。 您可以將其他設定保留為預設值。 然後，按一下 [套用]：

![新增 IoT Edge 裝置](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>新增 IoT Edge 裝置的替代方式

您也可以在解決方案加速器中，直接向 IoT 中樞執行個體註冊 IoT Edge 裝置。 您必須先知道解決方案加速器中 IoT 中樞的名稱，才能依照下列任何操作指南執行作業：

- [從 Azure 入口網站註冊新的 Azure IoT Edge 裝置](../iot-edge/how-to-register-device-portal.md)
- [使用 Azure CLI 來註冊新的 Azure IoT Edge 裝置](../iot-edge/how-to-register-device-cli.md)
- [從 Visual Studio Code 註冊新的 Azure IoT Edge 裝置](../iot-edge/how-to-register-device-vscode.md)

當您直接與遠端監視解決方案加速器的 IoT 中樞註冊裝置時，它會列在**Device Explorer** web UI 中的頁面。

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

您必須先在真實裝置上安裝 IoT Edge 執行階段，才可以將模組部署到 Edge 裝置。 下列操作指南說明如何在常見裝置平台上安裝執行階段：

- [在 Linux (x64) 上安裝 Azure IoT Edge 執行階段](../iot-edge/how-to-install-iot-edge-linux.md)
- [在 Linux (ARM32v7/armhf) 上安裝 Azure IoT Edge 執行階段](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [在 Windows 上安裝要與 Windows 容器搭配使用的 Azure IoT Edge 執行階段](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [在 Windows 上安裝要與 Linux 容器搭配使用的 Azure IoT Edge 執行階段](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [在 Windows IoT 核心版上安裝 IoT Edge 執行階段](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>後續步驟

您現在已備妥 IoT Edge 裝置，下一個步驟是將模組部署到該裝置。 請參閱[在遠端監視解決方案加速器中匯入 IoT Edge 套件](iot-accelerators-remote-monitoring-import-edge-package.md)
