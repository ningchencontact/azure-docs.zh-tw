---
title: 包含檔案
description: 包含檔案
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202690"
---
### <a name="delete-local-resources"></a>刪除本機資源

如果您想要從裝置中移除 IoT Edge 執行階段和相關資源，請使用您的裝置作業系統所適用的命令。 

#### <a name="windows"></a>Windows

解除安裝 IoT Edge 執行階段。

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

IoT Edge 執行階段移除後，它所建立的容器隨即停止，但仍會存在於您的裝置上。 檢視所有容器。

   ```powershell
   docker ps -a
   ```

刪除已在您的裝置上建立的執行階段容器。

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

藉由參照容器名稱，刪除在 `docker ps` 輸出中列出的任何其他容器。 

#### <a name="linux"></a>Linux

移除 IoT Edge 執行階段。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

IoT Edge 執行階段移除後，它所建立的容器隨即停止，但仍會存在於您的裝置上。 檢視所有容器。

   ```bash
   sudo docker ps -a
   ```

刪除已在您的裝置上建立的執行階段容器。

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

藉由參照容器名稱，刪除在 `docker ps` 輸出中列出的任何其他容器。 

移除容器執行階段。

   ```bash
   sudo apt-get remove --purge moby
   ```