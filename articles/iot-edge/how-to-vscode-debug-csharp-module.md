---
title: "使用 Visual Studio Code 以 Azure IoT Edge 進行 C# 模組偵錯 | Microsoft Docs"
description: "在 VS Code 中使用 Azure IoT Edge 進行 C# 模組偵錯"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 1ab67cd8aaf59cde3157fcb877ce13f10cb432bb
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2018
---
# <a name="use-visual-studio-code-to-debug-c-module-with-azure-iot-edge"></a>使用 Visual Studio Code 以 Azure IoT Edge 進行 C# 模組偵錯
本文會提供詳細指示，說明如何使用 [Visual Studio Code](https://code.visualstudio.com/) 作為主要開發工具來進行 IoT Edge 模組偵錯。

## <a name="prerequisites"></a>先決條件
本教學課程假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發機器。 IoT Edge 裝置可以是其他實體裝置，您也可以在開發機器上模擬 IoT Edge 裝置。

開始使用本指引之前，請確定您已完成下列教學課程。
- [使用 Visual Studio Code 以 Azure IoT Edge 開發 C# 模組](how-to-vscode-develop-csharp-module.md)

完成前述教學課程之後，您應該先準備好下列項目：
- 在您的開發機器上執行的本機 Docker 登錄。 建議您使用本機 Docker 登錄作為原型並用於測試。
- 包含最新篩選模組程式碼的 `Program.cs` 檔案。
- 適用於感應器模組和篩選模組的已更新 `deployment.json` 檔案。
- 在您的開發機器上執行的 Edge 執行階段。

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>建置用於偵錯的 IoT Edge 模組
1. 若要開始偵錯，您必須使用 **dockerfile.debug** 重建您的 docker 映像，並再次部署 Edge 解決方案。 在 VS Code 檔案總管中，按一下 Docker 資料夾將其開啟。 接著按一下 `linux-x64` 資料夾，以滑鼠右鍵按一下 [Dockerfile.debug]，然後按一下 [建置 IoT Edge 模組的 Docker 映像]。

    ![建置偵錯映像](./media/how-to-debug-csharp-module/build-debug-image.png)

3. 在 [選取資料夾] 視窗中，瀏覽至或是輸入 `./bin/Debug/netcoreapp2.0/publish`。 按一下 [選取資料夾為 EXE_DIR]。
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filtermodule:latest`。 如果您是要部署到本機登錄，它應該是 `localhost:5000/filtermodule:latest`。
5. 將映像推送到您的 Docker 存放庫。 使用 [Edge: 推送 IoT Edge 模組 Docker 映像] 命令，然後在 VS Code 視窗頂端的快顯文字方塊中輸入映像 URL。 使用您在上述步驟中使用的相同映像 URL。
6. 您可以重複使用 `deployment.json` 進行重新部署。 在命令選擇區中，輸入並選取 [Edge: 重新啟動 Edge]，讓篩選模組以偵錯版本執行。

## <a name="start-debugging-in-vs-code"></a>在 VS Code 中開始偵錯
1. 移至 VS Code 偵錯視窗。 按 **F5**，然後選取 [IoT Edge(.Net Core)]

    ![按 F5](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. 在 `launch.json` 中，瀏覽至 [偵錯 IoT Edge 自訂模組 (.NET Core)] 區段，填寫 `pipeArgs` 底下的 `<container_name>`。在本教學課程中，它應該是 `filtermodule`。

    ![修改 pipeArgs](./media/how-to-debug-csharp-module/f5-debug-option.png)

3. 瀏覽至 Program.cs。 在 `method static async Task<MessageResponse> FilterModule(Message message, object userContext)` 中新增中斷點。
4. 再按一次 **F5**。 然後選取所要連結的流程。 在本教學課程中，流程名稱應該是 `FilterModule.dll`

    ![連結流程](./media/how-to-debug-csharp-module/attach-process.png)

5. 在 VS Code 偵錯視窗中，您可以在左面板中看到變數。 

> [!NOTE]
> 以上範例示範如何對容器上的 .Net Core IoT Edge 模組進行偵錯。 它是以 `Dockerfile.debug` 的偵錯版本為基礎，其中包含建置時容器映像中的 VSDBG (.NET Core 命令列偵錯工具)。 完成 C# 模組偵錯之後，建議您針對生產就緒 IoT Edge 模組直接使用或自訂無 VSDBG 的 `Dockerfile`。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立了 IoT Edge 模組並加以部署以進行偵錯，然後在 VS Code 中開始偵錯。 您可以繼續進行下列任一個教學課程，以了解在 VS Code 中開發 Azure IoT Edge 時的其他案例。 

> [!div class="nextstepaction"]
> [在 VS Code 中開發和部署 C# 模組](how-to-vscode-develop-csharp-module.md)
