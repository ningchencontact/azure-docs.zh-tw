---
title: 在 VS Code 中管理多個 Azure IoT Edge 模組 | Microsoft Docs
description: 使用 Visual Studio Code 來開發使用多個模組的 IoT Edge 方案。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166347"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>在 Visual Studio Code 中使用多個模組來開發 IoT Edge 方案 - 預覽
您可以使用 Visual Studio Code，利用多個模組來開發 IoT Edge 方案。 本文會逐步引導您建立、更新和部署 IoT Edge 解決方案，此解決方案會在 Visual Studio Code 中，於模擬的 IoT Edge 裝置上透過管道傳送感應器資料。 

## <a name="prerequisites"></a>先決條件

若要完成本文中的所有步驟，請先準備好下列先決條件︰

- [Visual Studio Code](https://code.visualstudio.com/) 
- [適用於 Visual Studio Code 的 Azure IoT Edge 延伸模組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) \(英文\) 
- [C# for Visual Studio Code (由 OmniSharp 提供技術支援) 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) \(英文\) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) \(英文\) 
- AzureIoTEdgeModule 範本 (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 至少具有一部 IoT Edge 裝置的作用中 IoT 中樞


* [適用於 VS Code 的 Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) \(英文\) 以及總管整合，以用來管理映像和容器。


## <a name="prepare-your-first-iot-edge-solution"></a>準備您的第一個 IoT Edge 方案
1. 在 VS Code 命令選擇區中，輸入並執行命令**Edge: New IoT Edge solution** \(Edge：新增 IoT Edge 方案\)。 然後選取您的工作區資料夾、提供方案名稱 (預設名稱是 **EdgeSolution**)，然後建立 C# 模組 (**SampleModule**) 作為此方案中的第一個使用者模組。 您也需要為第一個模組指定 Docker 映像存放庫。 預設的映像存放庫會以本機 Docker 登錄 (`localhost:5000/<first module name>`) 為基礎。 您也可以將它變更為 Azure 容器登錄或 Docker 中樞。

   > [!NOTE]
   > 如果您使用的是本機 Docker 登錄，請在您的主控台視窗中輸入命令 `docker run -d -p 5000:5000 --restart=always --name registry registry:2` 來確定該登錄正在執行。

2. VS Code 視窗會載入您的 IoT Edge 方案工作區。 根資料夾包含 `modules` 資料夾、`.vscode` 資料夾，以及部署資訊清單範本檔案。 您可以在 `.vscode` 資料夾中查看偵錯設定。 所有使用者模組代碼都將是 `modules` 資料夾下的子資料夾。 `deployment.template.json` 是部署資訊清單範本。 此檔案中的部分參數將會從 `module.json` (存在於每個模組資料夾中) 進行剖析。

3. 將您的第二個模組新增至這個方案的專案。 這次請輸入並執行 **Edge: Add IoT Edge module** \(Edge：新增 IoT Edge 模組\)，然後選取要更新的部署範本檔案。 接著選取名稱為 **SampleFunction** 的 **Azure 函式 - C#**，以及其 Docker 映像存放庫。

4. 開啟 `deployment.template.json` 檔案，並確認它會宣告執行階段以外的三個模組。 訊息將會從 `tempSensor` 模組產生，並將透過 `SampleModule` 和 `SampleFunction` 直接進行管道傳送，然後傳送至您的 IoT 中樞。 
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
1. 在 VS Code 命令選擇區中，輸入並執行命令**Edge: Build IoT Edge solution** \(Edge：建置 IoT Edge 方案\)。 根據每個模組資料夾中的 `module.json` 檔案，此命令會開始建置、容器化及推送每個模組 Docker 映像。 然後它會將必要值傳送至 `deployment.template.json`，並產生 `deployment.json` 檔案，其中具有來自 `config` 資料夾的資訊。 您可以在 VS Code 整合終端機中看到建置進度。

2. 在 Azure IoT 中樞裝置總管中，以滑鼠右鍵按一下 IoT Edge 裝置識別碼，然後選取 [Create deployment for Edge device] \(針對 Edge 裝置建立部署\)。 選取 `config` 資料夾下的 `deployment.json`。 接著，您可以看到已順利建立部署，而且 VS Code 整合式終端機中會有部署識別碼。

3. 如果您是在開發機器上模擬 IoT Edge 裝置，您會看到所有模組映像容器都將在數分鐘內啟動。

## <a name="view-generated-data"></a>檢視產生的資料

1. 若要監視抵達 IoT 中樞的資料，請選取 [檢視] > [命令選擇區...]，然後搜尋 [IoT: 開始監視 D2C 訊息]。 
2. 若要停止監視資料，請在命令選擇區中使用 **IoT: Stop monitoring D2C message** 命令。 

## <a name="next-steps"></a>後續步驟

了解在 Visual Studio Code 中開發 Azure IoT Edge 的其他案例：

* [在 VS Code 中進行 C# 模組偵錯](how-to-vscode-debug-csharp-module.md)
* [在 VS Code 中進行 C# 函式偵錯](how-to-vscode-debug-azure-function.md)