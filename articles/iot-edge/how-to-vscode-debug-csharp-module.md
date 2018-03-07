---
title: "使用 Visual Studio Code 以 Azure IoT Edge 進行 C# 模組偵錯 | Microsoft Docs"
description: "在 Visual Studio Code 中使用 Azure IoT Edge 來進行 C# 模組偵錯。"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5ed517cf8d70cd279a55b79ad448709116cf511b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>使用 Visual Studio Code 搭配 Azure IoT Edge 進行 C# 模組偵錯
本文提供詳細的指示，說明如何使用 [Visual Studio Code](https://code.visualstudio.com/) 作為主要開發工具來進行 Azure IoT Edge 模組偵錯。

## <a name="prerequisites"></a>先決條件
本教學課程假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 您的 IoT Edge 裝置可以是其他實體裝置，或是您也可以在開發機器上模擬 IoT Edge 裝置。

開始使用本指引之前，請先完成下列教學課程：
- [使用 Visual Studio Code 以 Azure IoT Edge 開發 C# 模組](how-to-vscode-develop-csharp-module.md)

完成上述教學課程之後，您應該會備妥下列項目：
- 在您的開發機器上執行的本機 Docker 登錄。 這適用於建立原型和測試用途。
- 包含最新篩選模組程式碼的 `Program.cs` 檔案。
- 適用於感應器和篩選模組的已更新 `deployment.json` 檔案。
- 在您開發機器上執行的 IoT Edge 執行階段。

## <a name="build-your-iot-edge-module-for-debugging"></a>建置用於偵錯的 IoT Edge 模組
1. 若要開始偵錯，請使用 **dockerfile.debug** 來重建您的 Docker 映像，然後重新部署 IoT Edge 解決方案。 在「Visual Studio Code 總管」中，選取 [Docker] 資料夾來開啟它。 接著選取 [linux-x64] 資料夾，在 [Dockerfile.debug] 上按一下滑鼠右鍵，然後選取 [建置 IoT Edge 模組 Docker 映像]。

    ![VS Code 總管的螢幕擷取畫面](./media/how-to-debug-csharp-module/build-debug-image.png)

3. 在 [選取資料夾] 視窗中，瀏覽至或輸入 **./bin/Debug/netcoreapp2.0/publish**。 接著選取 [選取資料夾為 EXE_DIR]。
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filtermodule:latest`。 如果您要部署到本機登錄，它應該是：`localhost:5000/filtermodule:latest`。
5. 將映像推送到您的 Docker 存放庫。 使用 [Edge: 推送 IoT Edge 模組 Docker 映像] 命令，然後在 VS Code 視窗頂端的快顯文字方塊中輸入映像 URL。 使用您在上述步驟中使用的相同映像 URL。
6. 您可以重複使用 `deployment.json` 進行重新部署。 在命令選擇區中，輸入並選取 [Edge: 重新啟動 Edge]，讓篩選模組以偵錯版本執行。

## <a name="start-debugging-in-vs-code"></a>在 VS Code 中開始偵錯
1. 移至 VS Code 偵錯視窗。 按 **F5**，然後選取 [IoT Edge(.Net Core)]。

    ![VS Code 偵錯視窗的螢幕擷取畫面](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. 在 `launch.json` 中，瀏覽至 [針對 IoT Edge 自訂模組進行偵錯 (.NET Core)] 區段。 在 [pipeArgs] 底下，填入 `<container_name>`。 在本教學課程中，應該是 `filtermodule`。

    ![VS Code launch.json 的螢幕擷取畫面](./media/how-to-debug-csharp-module/add-container-name.png)

3. 瀏覽至 **Program.cs**。 在 `method static async Task<MessageResponse> FilterModule(Message message, object userContext)` 中新增中斷點。
4. 再次按 **F5**，然後選取要連結的程序。 在本教學課程中，程序名稱應該是 `FilterModule.dll`。

    ![VS Code 偵錯視窗的螢幕擷取畫面](./media/how-to-debug-csharp-module/attach-process.png)

5. 在 VS Code 偵錯視窗中，您可以在左面板中看到變數。 

> [!NOTE]
> 上述範例說明如何針對容器上的 .Net Core IoT Edge 模組進行偵錯。 它是以 `Dockerfile.debug` 的偵錯版本為基礎，其中包含建置它時您容器映像中的 VSDBG (.NET Core 命令列偵錯工具)。 完成 C# 模組偵錯之後，建議您針對已可供生產環境使用的 IoT Edge 模組，直接使用 `Dockerfile` 或將其自訂成不含 VSDBG。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 IoT Edge 模組並部署它來進行偵錯。 您在 VS Code 中開始對它進行偵錯。 在 VS Code 中開發 Azure IoT Edge 時，若要了解其他案例，請參閱： 

> [!div class="nextstepaction"]
> [在 VS Code 中開發和部署 C# 模組](how-to-vscode-develop-csharp-module.md)
