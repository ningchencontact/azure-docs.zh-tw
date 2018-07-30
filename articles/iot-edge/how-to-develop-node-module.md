---
title: Azure IoT Edge 的偵錯 Node.js 模組 | Microsoft Docs
description: 使用 Visual Studio Code 來開發適用於 Azure IoT Edge 的 Node.js 模組以及針對其進行偵錯
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8032fd2a0150597c55178648511c80233e63a911
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054721"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>使用適用於 Visual Studio Code 的 Azure IoT Edge 來開發 Node.js 模組以及針對其進行偵錯

您可以將商務邏輯轉換成 Azure IoT Edge 的模組，以將您的商務邏輯傳送到邊緣來運作。 本文提供詳細指示，說明如何使用 Visual Studio Code (VS Code) 作為主要開發工具來開發 C# 模組。

## <a name="prerequisites"></a>必要條件
本文假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 您的 IoT Edge 裝置可以是其他實體裝置，或是您也可以在開發機器上模擬 IoT Edge 裝置。

> [!NOTE]
> 本偵錯教學課程說明如何在模組容器中附加一個流程，並使用 VS Code 進行偵錯。 您可以在 linux-amd64、windows 和 arm32 容器中針對 Node.js 模組進行偵錯。 如果您不熟悉 Visual Studio Code 的偵錯功能，請參閱[偵錯](https://code.visualstudio.com/Docs/editor/debugging)。 

因為本文使用 Visual Studio Code 作為主要開發工具，因此請安裝 VS Code，然後新增必要的擴充：
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker 擴充](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

若要建立模組，您需要 Node.js ，它包含 npm 可以建置專案資料夾，另外還需要 Docker 來建置模組映像，最後需要容器登錄來保存模組映像：
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或 [Docker 中樞](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >您可以使用本機 Docker 登錄作為原型並用於測試，而非使用雲端登錄。 

若要在裝置上測試您的模組，您需要一個作用中 IoT 中樞，而且該中樞內至少必須有一個 IoT Edge 裝置。 如果要使用您的電腦作為 IoT Edge 裝置，您可以遵循 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 教學課程中的步驟執行。 

## <a name="create-a-new-solution-template"></a>建立新的解決方案範本

下列步驟會示範如何使用 Visual Studio Code 和 Azure IoT Edge 擴充，來建立以 .NET Core 2.0 為基礎的 IoT Edge 模組。 您開始建立解決方案，然後在該解決方案中產生第一個模組。 每個解決方案都可以包含多個模組。 

1. 在 Visual Studio Code 中，選取 [檢視]  >  [整合式終端機]。
2. 在整合式終端機中，輸入下列命令來安裝 (或更新) 適用於 Node.js 的最新版本 Azure IoT Edge 模組範本：

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. 在 Visual Studio Code 中，選取 [檢視] > [命令選擇區]。 
4. 在命令選擇區中，輸入並執行命令 **Azure IoT Edge: New IoT Edge Solution**。

   ![執行新的 IoT Edge 解決方案](./media/how-to-develop-csharp-module/new-solution.png)

5. 瀏覽至您要建立新解決方案所在的資料夾，然後按一下 [選取資料夾]。 
6. 提供解決方案的名稱。 
7. 選擇 [Node.js 模組] 作為解決方案中第一個模組的範本。
8. 提供模組的名稱。 選擇容器登錄內唯一的名稱。 
9. 提供模組的映像存放庫。 VS Code 會自動填寫模組名稱，因此您只需要將 **localhost:5000** 取代為自己的登錄資訊即可。 如果您使用本機 Docker 登錄來進行測試，則可以使用 localhost。 如果您使用 Azure Container Registry，則請使用登錄設定中的登入伺服器。 登入伺服器看起來像**\<登錄名稱\>.azurecr.io**。

VS Code 會採用您提供的資訊、建立 IoT Edge 解決方案，然後將它載入至新的視窗。

在解決方案中，您有三個項目： 
* 一個包含偵錯組態的 **.vscode** 資料夾。
* 一個 **modules** 資料夾，它包含每個模組的子資料夾。 現在您只有一個模組資料夾，但是可以在命令選擇區中，使用命令 **Azure IoT Edge: Add IoT Edge Module** 來新增更多模組資料夾。 
* 一個 **.env** 檔案，它會列出您的環境變數。 如果您將 ACR 作為自己的登錄，現在裡面會有 ACR 使用者名稱和密碼。 

   >[!NOTE]
   >環境檔案只會在您為模組提供了映像存放庫時才會建立。 如果您接受 localhost 預設值，並且在本機進行測試和偵錯，則不需要宣告環境變數。 

* 一個 **deployment.template.json** 檔案，它會列出新的模組以及一個範例 **tempSensor** 模組，此範例模組會模擬可用於測試的資料。 如需部署資訊清單運作方式的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。

## <a name="devlop-your-module"></a>開發您的模組

解決方案隨附的預設 Azure Function 程式碼位於：[模組] > \<[您的模組名稱]\> > [app.js]。 系統會設定模組和 deployment.template.json 檔案，讓您可以建置解決方案、將其推送至容器登錄，然後部署到裝置以開始測試，而不需要觸碰到任何程式碼。 模組是建置為僅採用來源的輸入 (在此案例中，是會模擬資料的 tempSensor 模組)，並且將其傳送到 IoT 中樞。 

當您準備要使用自己的程式碼自訂 Node.js 範本時，請使用 [Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md) 以建置模組，該模組會解決 IoT 解決方案的主要需求，例如安全性、裝置管理和可靠性。 

## <a name="build-and-deploy-your-module-for-debugging"></a>建置及部署您的模組來進行偵錯

在每個模組資料夾中，有不同容器類型的多個 Docker 檔案。 您可以使用任何副檔名為 **.debug** 的檔案來建置測試用模組。 目前，C# 模組只支援在 linux-amd64 容器中進行偵錯。

1. 在 VS Code 中，瀏覽至 `deployment.template.json` 檔案。 將 **deployment.template.json** 中的 Node.js 模組 createOptions 取代為以下內容並儲存此檔案： 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. 在 VS Code 命令選擇區中，輸入並執行命令 **Azure IoT Edge: Build IoT Edge solution**。
3. 從命令選擇區中，為您的解決方案選取 `deployment.template.json` 檔案。 
4. 在 Azure IoT 中樞裝置總管中，以滑鼠右鍵按一下 IoT Edge 裝置識別碼，然後選取 [針對 IoT Edge 裝置建立部署]。 
5. 開啟解決方案的 **config** 資料夾，然後選取 `deployment.json` 檔案。 按一下 [選取 Edge 部署資訊清單]。 

接著，您可以看到已順利建立部署，而且 VS Code 整合式終端機中會有部署識別碼。

您可以在 VS Code Docker 總管中檢查您的容器狀態，或透過在終端機中執行 `docker ps` 命令來檢查。

## <a name="start-debugging-nodejs-module-in-vs-code"></a>開始在 VS Code 中針對 Node.Js 模組進行偵錯

VS Code 會將偵錯設定資訊保留在您工作區中之 `.vscode` 資料夾的 `launch.json` 檔案中。 這個 `launch.json` 檔案在您建立新 IoT Edge 解決方案時產生。 每當您新增支援偵錯的模組時，此檔案都會更新。 

1. 瀏覽至 VS Code 偵錯檢視並選取模組的偵錯組態檔。

2. 瀏覽至 `app.js`。 在此檔案中新增中斷點。

3. 按一下 [開始偵錯] 按鈕或按 **F5**，然後選取要附加至的處理序。

4. 在 VS Code 偵錯檢視中，您可以在左面板中看到變數。 

上述範例說明如何針對容器上的 Node.js IoT Edge 模組進行偵錯。 這樣會在您的模組容器 createOptions 中新增公開連接埠。 完成 Node.js 模組偵錯之後，建議您針對已可供生產環境使用的 IoT Edge 模組，移除這些公開連接埠。

## <a name="next-steps"></a>後續步驟

建置模組之後，了解如何[從 Visual Studio Code 部署 Azure IoT Edge 模組](how-to-deploy-modules-vscode.md)

若要為您的 IoT Edge 裝置開發模組，請[了解及使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
