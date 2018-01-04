---
title: "使用 Visual Studio 程式碼偵錯 Azure 函式與 Azure IoT 邊緣 |Microsoft 文件"
description: "偵錯 C# VS 程式碼中的 Azure IoT 邊緣 Azure 函式"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4344a450d218a7424cd055cf086c1e4865c9af10
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>使用 Visual Studio 程式碼偵錯 Azure IoT 邊緣 Azure 函式

本文章提供詳細的指示使用[Visual Studio Code](https://code.visualstudio.com/)與偵錯 Azure IoT 邊緣函式的主要開發工具。

## <a name="prerequisites"></a>必要條件
本教學課程假設您使用的電腦或您的開發電腦以執行 Windows 或 Linux 的虛擬機器。 您的 IoT 邊緣裝置可能是另一個實體裝置，或您可以在開發電腦上模擬您的 IoT 邊緣裝置。

請確定您有已完成下列教學課程，才能啟動這項指引。
- [使用 Visual Studio 程式碼來開發並部署至 Azure IoT 邊緣 Azure 函式](how-to-vscode-develop-azure-function.md)

之後完成前述的教學課程，您應該準備好，下列項目
- 本機的 Docker 登錄在開發電腦上執行。 建議使用本機的 Docker 登錄原型和測試目的。
- `run.csx`以最新的篩選器函式程式碼檔案。
- 更新`deployment.json`感應器模組及篩選函式模組的檔案。
- 在開發電腦上執行邊緣執行階段。

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>建置您的 IoT 邊緣模組的偵錯用途
1. 若要開始偵錯，您必須使用**dockerfile.debug**重建您的 docker 映像，並重新部署邊緣解決方案。 在 VS 程式碼總管 中，按一下 Docker 資料夾，將它開啟。 然後按一下 `linux-x64`資料夾中，以滑鼠右鍵按一下**Dockerfile.debug**，然後按一下**建置 IoT 邊緣模組 Docker 映像**。
3. 在**選取資料夾**視窗中，瀏覽至**FilterFunction**專案，然後按一下**選取資料夾 EXE_DIR**。
4. 在 VS Code 視窗頂端的快顯文字方塊中，輸入映像名稱。 例如：`<your container registry address>/filterfunction:latest`。 如果您要部署到本機登錄中，它應該是`localhost:5000/filterfunction:latest`。
5. 將映像推送到您的 Docker 存放庫。 使用**邊緣： Push IoT 邊緣模組 Docker 映像**命令，並在 VS 程式碼視窗頂端快顯的文字方塊中輸入影像 URL。 使用相同的影像 URL 中使用上述步驟。
6. 您可以重複使用`deployment.json`重新部署。 在命令調色盤中，輸入，然後選取**邊緣： 重新啟動邊緣**取得您篩選條件的函式執行偵錯版本。

## <a name="start-debugging-in-vs-code"></a>VS Code 在啟動偵錯
1. 移至 VS Code 偵錯視窗。 按**F5**選取**IoT Edge(.Net Core)**
2. 在`launch.json`，瀏覽至**偵錯 IoT 邊緣函式 (.NET Core)**區段，並填寫`<container_name>`下`pipeArgs`。它應該是`filterfunction`在本教學課程。
3. 瀏覽至 run.csx。 函式中加入中斷點。
4. 按**F5**一次。 並選取要附加至處理序。
5. 在 VS 偵錯程式碼視窗中，您可以看到左面板中的變數。 

> [!NOTE]
> 上述範例示範如何偵錯.Net Core IoT 邊緣函式，在容器上。 它根據的偵錯版本`Dockerfile.debug`，容器映像中包括 VSDBG （.NET Core 命令列偵錯工具） 建立它時。 我們建議您直接使用，或自訂`Dockerfile`沒有 VSDBG 備妥 IoT 邊緣函式之後您完成偵錯 C# 函式。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您必須建立 Azure 函式和部署 IoT 邊緣偵錯用途，並啟動其偵錯 VS 程式碼中。 您可以繼續閱讀下列教學課程來了解其他案例時開發 VS 程式碼中的 Azure IoT 邊緣之一。 

> [!div class="nextstepaction"]
> [開發和部署 C# VS 程式碼中的模組](how-to-vscode-develop-csharp-module.md)

