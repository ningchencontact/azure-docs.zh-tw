---
title: "使用 Visual Studio Code 為 Azure IoT Edge 上的 Azure Functions 偵錯 | Microsoft Docs"
description: "在 VS Code 中偵錯 Azure IoT Edge 上的 C# Azure Functions"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: db86a08a19e97f8f415849aa060fe87d77cccf68
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>使用 Visual Studio Code 為 Azure IoT Edge 上的 Azure Functions 偵錯

本文提供使用 [Visual Studio Code](https://code.visualstudio.com/) 作為主要開發工具，偵錯 IoT Edge 上 Azure Functions 的詳細指示。

## <a name="prerequisites"></a>先決條件
本教學課程假設您使用執行 Windows 或 Linux的電腦或虛擬機器作為您的開發電腦。 您的 IoT Edge 裝置可能是另一個實體裝置，或者您可以在開發電腦上模擬您的 IoT Edge 裝置。

開始使用本指引之前，請確定您已完成下列教學課程。
- [使用 Visual Studio Code 開發 Azure Functions 並部署至 Azure IoT Edge](how-to-vscode-develop-azure-function.md)

完成前述教學課程之後，您應該先準備好下列項目：
- 在您的開發機器上執行的本機 Docker 登錄。 建議您使用本機 Docker 登錄作為原型和測試目的。
- 包含最新篩選器函式程式碼的 `run.csx` 檔案。
- 最新感應器模組及篩選器函式模組的 `deployment.json` 檔案。
- 在您的開發機器上執行的 Edge 執行階段。

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>建置用於偵錯的 IoT Edge 模組
1. 若要開始偵錯，您必須使用 **dockerfile.debug** 重建您的 docker 映像，並再次部署 Edge 解決方案。 在 VS Code 檔案總管中，按一下 Docker 資料夾將其開啟。 按一下 `linux-x64` 資料夾，以滑鼠右鍵按一下 [Dockerfile.debug]，然後按一下 [建置 IoT Edge 模組的 Docker 映像]。

    ![建置偵錯映像](./media/how-to-debug-csharp-function/build-debug-image.png)

2. 在 [選取資料夾] 視窗中，瀏覽至 [FilterFunction] 專案，然後按一下 [選取資料夾作為 EXE_DIR]。
3. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filterfunction:latest`。 如果您是部署到本機登錄，它應該是 `localhost:5000/filterfunction:latest`。

    ![推送映像](./media/how-to-debug-csharp-function/push-image.png)

4. 將映像推送到您的 Docker 存放庫。 使用 **Edge: Push IoT Edge module Docker image** 命令，在 VS Code 視窗頂端的快顯文字方塊中輸入映像 URL。 使用您在上述步驟中使用的相同映像 URL。
5. 您可以重複使用 `deployment.json` 進行重新部署。 在命令調色盤中，輸入然後選取 **Edge: Restart Edge**，讓您的篩選器函式以偵錯版本執行。

## <a name="start-debugging-in-vs-code"></a>在 VS Code 中啟動偵錯
1. 移至 VS Code 偵錯視窗。 按 **F5**，選取 [IoT Edge(.Net Core)]

    ![按 F5](./media/how-to-debug-csharp-function/f5-debug-option.png)

2. 在 `launch.json` 中，瀏覽至 [偵錯 IoT Edge 函式 (.NET Core)] 區段，填寫 `pipeArgs` 底下的 `<container_name>`。在本教學課程中，它應該是 `filterfunction`。

    ![更新 launch.json](./media/how-to-debug-csharp-function/update-launch-json.png)

3. 瀏覽至 run.csx。 在函式中新增中斷點。
4. 瀏覽至偵錯視窗 (Ctrl + Shift + D)，從下拉式清單中選擇 [偵錯 IoT Edge 函式 (.NET Core)]。 

    ![選取偵錯模式](./media/how-to-debug-csharp-function/choose-debug-mode.png)

5. 按一下 [開始偵錯] 按鈕或按**F5**，並選取要附加至哪個處理序。

    ![附加函式處理序](./media/how-to-debug-csharp-function/attach-function-process.png)

6. 在 VS Code 偵錯視窗中，您可以在左面板中看到變數。 

> [!NOTE]
> 以上範例示範如何偵錯容器上的 .Net Core IoT Edge 函式。 它以 `Dockerfile.debug` 的偵錯版本為依據，此版本包含建立時容器映像中的 VSDBG (.NET Core 命令列偵錯工具)。 完成 C# 函式偵錯之後，我們建議您直接使用，或針對生產就緒 IoT Edge 函式自訂無 VSDBG 的 `Dockerfile`。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您建立了 Azure 函式並為了偵錯將它部署到 IoT Edge，然後在 VS Code 中開始偵錯。 您可以繼續閱讀下列任一個教學課程，以了解在 VS Code 中開發 Azure IoT Edge 時的其他案例。 

> [!div class="nextstepaction"]
> [在 VS Code 中開發和部署 C# 模組](how-to-vscode-develop-csharp-module.md)

