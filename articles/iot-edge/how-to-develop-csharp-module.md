---
title: 針對適用於 Azure IoT Edge 的 C# 模組進行偵錯 | Microsoft Docs
description: 使用 Visual Studio Code 來開發、建置適用於 Azure IoT Edge 的 C# 模組以及針對其進行偵錯
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a895f21bc061763b1d5d45b2bedb44fc932190dc
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345209"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>使用 Visual Studio Code 來開發適用於 Azure IoT Edge 的 C# 模組以及針對其進行偵錯

您可以將商務邏輯轉換成 Azure IoT Edge 的模組。 本文說明如何使用 Visual Studio Code (VS Code) 作為主要工具，來開發 C# 模組並對其進行偵錯。

## <a name="prerequisites"></a>必要條件
本文假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 您的 IoT Edge 裝置可以是另一部實體裝置。 您也可以在開發電腦上模擬 IoT Edge 裝置。

> [!NOTE]
> 本偵錯文章示範如何在模組容器中附加一個流程，並使用 VS Code 進行偵錯。 您只能在 Linux amd64 容器中進行 C# 模組偵錯。 如果您不熟悉 Visual Studio Code 的偵錯功能，請參閱[偵錯](https://code.visualstudio.com/Docs/editor/debugging)。 

因為本文使用 Visual Studio Code 作為主要開發工具，所以請安裝 VS Code。 然後新增必要的擴充功能：
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 擴充功能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker 擴充功能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

若要建立模組，您需要 .NET 來建置專案資料夾，另外還需要 Docker 來建置模組映像，最後需要容器登錄來保存模組映像：
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* 開發電腦上的 [Docker Community Edition](https://docs.docker.com/install/)。 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 或 [Docker 中樞](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > 您可以使用本機 Docker 登錄作為原型並用於測試，而非使用雲端登錄。 

若要在裝置上測試模組，您需要一個有效的 IoT 中樞，而且該中樞中至少必須有一個 IoT Edge 裝置。 若要使用您的電腦作為 IoT Edge 裝置，請遵循 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 快速入門中的步驟。 

## <a name="create-a-new-solution-template"></a>建立新的解決方案範本

採取這些步驟以使用 Visual Studio Code 和 Azure IoT Edge 擴充功能，來建立以 .NET Core 2.0 為基礎的 IoT Edge 模組。 首先要建立解決方案，然後在該解決方案中產生第一個模組。 每個解決方案都可以包含多個模組。 

1. 在 Visual Studio Code 中，選取 [檢視] > [整合式終端機]。
3. 選取 [檢視] > [命令選擇區]。 
4. 在命令選擇區中，輸入並執行命令 **Azure IoT Edge: New IoT Edge Solution**。

   ![執行新的 IoT Edge 解決方案](./media/how-to-develop-csharp-module/new-solution.png)

5. 瀏覽至要用來建立新解決方案的資料夾。 選擇 [選取資料夾]。 
6. 輸入解決方案的名稱。 
7. 選取 [C# 模組] 作為解決方案中第一個模組的範本。
8. 輸入模組的名稱。 選擇容器登錄內唯一的名稱。 
9. 提供模組映像存放庫的名稱。 VS Code 會自動以 **localhost:5000** 填入模組名稱。 請使用您自己的登錄資訊加以取代。 如果您使用本機 Docker 登錄來進行測試，則可以使用 **localhost**。 如果您使用 Azure Container Registry，則請使用登錄設定中的登入伺服器。 登入伺服器看起來像**\<登錄名稱\>.azurecr.io**。

VS Code 會採用您提供的資訊、建立 IoT Edge 解決方案，然後將其載入至新的視窗。

   ![檢視 IoT Edge 方案](./media/how-to-develop-csharp-module/view-solution.png)

解決方案中有四個項目： 
* 一個包含偵錯組態的 **.vscode** 資料夾。
* 一個 **modules** 資料夾，其中包含每個模組的子資料夾。 此時，您只有一個資料夾。 但是您可以在命令選擇區中使用 **Azure IoT Edge: Add IoT Edge Module** 命令來新增更多項目。 
* 一個 **.env** 檔案，會列出您的環境變數。 如果您的登錄是 Azure Container Registry，您會有 Azure Container Registry 使用者名稱和密碼。 

   >[!NOTE]
   >環境檔案只會在您為模組提供了映像存放庫時才會建立。 如果您接受 localhost 預設值，並且在本機進行測試和偵錯，則不需要宣告環境變數。 

* 一個 **deployment.template.json** 檔案，會列出新的模組以及一個範例 **tempSensor** 模組，此範例模組會模擬可用於測試的資料。 如需部署資訊清單運作方式的詳細資訊，請參閱[了解如何使用部署資訊清單以部署模組和建立路由](module-composition.md)。 

## <a name="devlop-your-module"></a>開發您的模組

解決方案隨附的預設 Azure Function 程式碼位於：[模組] > \<[您的模組名稱]\> > [Program.cs]。 系統會設定模組和 deployment.template.json 檔案，讓您可以建置解決方案、將其推送至容器登錄，然後部署到裝置以開始測試，而不需要觸碰到任何程式碼。 模組會建置為僅採用來源的輸入 (在此案例中，是會模擬資料的 tempSensor 模組)，並且將其傳送到 IoT 中樞。 

當您準備要使用自己的程式碼自訂 C# 範本時，請使用 [Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md) 以建置模組，該模組會滿足 IoT 解決方案的主要需求，例如安全性、裝置管理和可靠性。 

## <a name="build-and-deploy-your-module-for-debugging"></a>建置及部署您的模組來進行偵錯

在每個模組資料夾中，會有不同容器類型的多個 Docker 檔案。 請使用任何副檔名為 **.debug** 的檔案來建置測試用模組。 目前，C# 模組只支援在 Linux amd64 容器中進行偵錯。

1. 在 VS Code 中，瀏覽至 `deployment.template.json` 檔案。 在結尾加上 **.debug**，以更新您的功能映像 URL。

   ![請將 **.debug** 新增至映像名稱](./media/how-to-develop-csharp-module/image-debug.png)

2. 在 VS Code 命令選擇區中，輸入並執行 **Edge: Build IoT Edge solution** 命令。
3. 從命令選擇區中，為您的解決方案選取 `deployment.template.json` 檔案。 
4. 在 Azure IoT 中樞 Device Explorer 中，以滑鼠右鍵按一下 IoT Edge 裝置識別碼。 然後選取 [為 IoT Edge 裝置建立部署]。 
5. 開啟解決方案的 **config** 資料夾。 然後選取 `deployment.json` 檔案。 選擇 [選取 Edge 部署資訊清單]。 

您會看到部署已順利建立，而且 VS Code 整合式終端機中會有部署識別碼。

在 VS Code Docker 總管中檢查您的容器狀態，或透過在終端機中執行 `docker ps` 命令來檢查。

## <a name="start-debugging-c-module-in-vs-code"></a>開始在 VS Code 中針對 C# 模組進行偵錯
VS Code 會將偵錯設定資訊保留在您工作區中之 `.vscode` 資料夾的 `launch.json` 檔案中。 這個 `launch.json` 檔案在您建立新 IoT Edge 解決方案時產生。 每當您新增支援偵錯的模組時，此檔案都會更新。 

1. 瀏覽至 VS Code 偵錯檢視。 為您的模組選取偵錯組態檔。 偵錯選項名稱應該類似 **ModuleName 遠端偵錯 (.NET Core)**

   ![選取偵錯組態](./media/how-to-develop-csharp-module/debug-config.png).

2. 瀏覽至 `program.cs`。 在此檔案中新增中斷點。

3. 選取 [開始偵錯] 或選取 **F5**。 選取所要連結的流程。

4. 在 VS Code 偵錯檢視中，您可以在左面板中看到變數。 

> [!NOTE]
> 這個範例說明如何針對容器上的 .Net Core IoT Edge 模組進行偵錯。 它是以 `Dockerfile.debug` 的偵錯版本為基礎，其中包含建置它時容器映像中的 .NET Core 命令列偵錯工具 VSDBG。 針對 C# 模組進行偵錯之後，建議您針對已可供生產環境使用的 IoT Edge 模組，直接使用 `Dockerfile` 或將其自訂成不含 VSDBG。

## <a name="next-steps"></a>後續步驟

建置模組之後，請了解如何[從 Visual Studio Code 部署 Azure IoT Edge 模組](how-to-deploy-modules-vscode.md)。

若要為您的 IoT Edge 裝置開發模組，請[了解及使用 Azure IoT 中樞 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
