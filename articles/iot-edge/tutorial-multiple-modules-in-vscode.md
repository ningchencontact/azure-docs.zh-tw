---
title: 在 VS Code 中管理多個 Azure IoT Edge 模組 | Microsoft Docs
description: 使用 Visual Studio Code 來開發使用多個模組的 Azure IoT Edge 解決方案。
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763029"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>在 Visual Studio Code 中使用多個模組來開發 IoT Edge 解決方案 (預覽)

您可以使用 Visual Studio Code，利用多個模組來開發 Azure IoT Edge 解決方案。 本文說明如何建立、更新和部署 IoT Edge 解決方案，該解決方案會在 VS Code 中於模擬的 IoT Edge 裝置上透過管道傳送感應器資料。 

## <a name="prerequisites"></a>先決條件

若要完成本文中的步驟，請先準備好下列先決條件︰

- [Visual Studio Code](https://code.visualstudio.com/)
- [適用於 Visual Studio Code 的 Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# for Visual Studio Code (由 OmniSharp 提供技術支援) 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)
- AzureIoTEdgeModule 範本 (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 至少具有一部 IoT Edge 裝置的作用中 IoT 中樞

您也需要整合 [適用於 VS Code 的 Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) 與 Azure IoT 中樞總管，才能管理映像和容器。

## <a name="prepare-your-first-iot-edge-solution"></a>準備您的第一個 IoT Edge 方案

1. 在 VS Code 的 [命令選擇區]  中，輸入並執行 [Edge: 新增 IoT Edge 解決方案] 命令。 選取您的工作區資料夾，並提供解決方案名稱 (預設值為 EdgeSolution)。 建立 C# 模組 (具有名稱 **SampleModule**) 作為此解決方案中的第一個使用者模組。 您也需要為第一個模組指定 Docker 映像存放庫。 預設的映像存放庫是以本機 Docker 登錄 (**localhost:5000/<first module name>**) 為基礎。 您可以將它變更為 Azure Container Registry 或 Docker 中樞。

   > [!NOTE]
   > 如果您使用本機 Docker 登錄，請確定該登錄正在執行。 在主控台視窗中輸入下列命令：
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. VS Code 視窗會載入您的 IoT Edge 方案工作區。 根資料夾包含 **modules** 資料夾、**.vscode** 資料夾，以及部署資訊清單範本檔案。 偵錯組態位於 .vscode 資料夾中。 所有使用者模組代碼都是 modules 資料夾的子資料夾。 deployment.template.json 檔案是部署資訊清單範本。 此檔案中的部分參數會從 module.json 檔案 (存在於每個模組資料夾中) 進行剖析。

3. 將您的第二個模組新增至此解決方案專案。 輸入並執行 [Edge: 新增 IoT Edge 模組] 命令。 選取要更新的部署範本檔案。 選取名稱為 **SampleFunction** 的 [Azure 函式 - C#]，以及其 Docker 映像存放庫。

4. 開啟 deployment.template.json 檔案。 確認該檔案會宣告三個模組和此執行階段。 訊息會產生自 tempSensor 模組。 訊息會透過 SampleModule 和 SampleFunction 模組直接進行管道傳送，然後傳送至您的 IoT 中樞。 

5. 使用下列內容來更新這些模組的路由：

   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. 儲存這個檔案。

## <a name="build-and-deploy-your-iot-edge-solution"></a>建置和部署您的 IoT Edge 方案

1. 在 VS Code 的 [命令選擇區] 中，輸入並執行 [Edge: 建置 IoT Edge 解決方案] 命令。 根據每個模組資料夾中的 module.json 檔案，此命令會開始建置、容器化及推送每個模組 Docker 映像。 此命令會接著將必要值傳遞至 deployment.template.json 檔案，並以 config 資料夾中的資訊產生 deployment.json 檔案。 VS Code 中的整合式終端機會顯示建立進度。

2. 在 Azure IoT 中樞的 [裝置總管] 中，以滑鼠右鍵按一下 IoT Edge 裝置識別碼，然後選取 [針對 Edge 裝置建立部署] 命令。 選取 config 資料夾中的 deployment.json 檔案。 VS Code 中的整合式終端機顯示已使用部署識別碼順利建立部署。

3. 如果您是在開發機器上模擬 IoT Edge 裝置，請注意所有模組映像容器都會在數分鐘內啟動。

## <a name="view-the-generated-data"></a>檢視所產生的資料

1. 若要監視送達 IoT 中樞的資料，請選取 [檢視] > [命令選擇區]。 然後選取 [IoT：開始監視 D2C 訊息] 命令。 
2. 若要停止監視資料，請在 [命令選擇區] 中使用 [IoT：停止監視 D2C 訊息] 命令。 

## <a name="next-steps"></a>後續步驟

了解在 Visual Studio Code 中使用 Azure IoT Edge 進行開發的其他案例：

* [在 VS Code 中進行 C# 模組偵錯](how-to-vscode-debug-csharp-module.md)
* [在 VS Code 中進行 C# 函式偵錯](how-to-vscode-debug-azure-function.md)
