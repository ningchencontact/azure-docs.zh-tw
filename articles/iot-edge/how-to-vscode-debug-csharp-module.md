---
title: 使用 Visual Studio Code 以 Azure IoT Edge 進行 C# 模組偵錯 | Microsoft Docs
description: 在 Visual Studio Code 中使用 Azure IoT Edge 來進行 C# 模組偵錯。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c2a1acd2c249bdbc92119bc92f055b095f318f00
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>使用 Visual Studio Code 搭配 Azure IoT Edge 進行 C# 模組偵錯
本文提供詳細的指示，說明如何使用 [Visual Studio Code](https://code.visualstudio.com/) 作為主要開發工具來進行 Azure IoT Edge 模組偵錯。

## <a name="prerequisites"></a>先決條件
本教學課程假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 您的 IoT Edge 裝置可以是其他實體裝置，或是您也可以在開發電腦上模擬 IoT Edge 裝置。

開始使用本指引之前，請先完成下列教學課程：
- [使用 Visual Studio Code 中的多個模組來開發 IoT Edge 解決方案](tutorial-multiple-modules-in-vscode.md)

完成上述教學課程之後，您應該會備妥下列項目：
- 在您的開發電腦上執行的本機 Docker 登錄。 建議您使用本機 Docker 登錄作為原型並用於測試。 您可以在每個模組資料夾中的 `module.json` 檔案中更新容器登錄。
- 具有 C# 模組子資料夾的 IoT Edge 解決方案專案工作區。
- 包含最新模組程式碼的 `Program.cs` 檔案。
- 在您的開發電腦上執行的 Edge 執行階段。

## <a name="build-your-iot-edge-c-module-for-debugging"></a>建置用於偵錯的 IoT Edge C# 模組
1. 若要開始偵錯，您必須使用 **Dockerfile.amd64.debug** 重建您的 docker 映像，並再次部署 Edge 解決方案。 在 VS Code 總管中，瀏覽至 `deployment.template.json` 檔案。 透過在結尾加上 `.debug` 以更新您的功能映像 URL。

2. 重建您的解決方案。 在 VS Code 命令選擇區中，輸入並執行命令**Edge：建置 IoT Edge 解決方案**。

3. 在 Azure IoT 中樞裝置總管中，以滑鼠右鍵按一下 IoT Edge 裝置 ID，然後選取 [Create deployment for Edge device] \(針對 Edge 裝置建立部署\)。 選取 `config` 資料夾下的 `deployment.json`。 接著，您可以看到已順利建立部署，而且 VS Code 整合式終端機中會有部署 ID。

> [!NOTE]
> 您可以在 VS Code Docker 總管中檢查您的容器狀態，或透過在終端機中執行 `docker images` 命令來檢查。

## <a name="start-debugging-c-module-in-vs-code"></a>開始在 VS Code 中針對 C# 模組進行偵錯
1. VS Code 會將偵錯設定資訊保留在您工作區中之 `.vscode` 資料夾的 `launch.json` 檔案中。 這個 `launch.json` 檔案是在建立新 IoT Edge 解決方案時所產生。 此外，這個檔案會在您每次加入支援偵錯的新模組時更新。 瀏覽到偵錯檢視並選取對應的偵錯設定檔案。
    ![選取偵錯設定](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. 瀏覽至 `program.cs`。 在此檔案中新增中斷點。

3. 按一下 [開始偵錯] 按鈕或按 **F5**，並選取要附加至哪個處理序。

4. 在 VS Code 偵錯檢視中，您可以在左面板中看到變數。 

> [!NOTE]
> 上述範例說明如何針對容器上的 .Net Core IoT Edge 模組進行偵錯。 它是以 `Dockerfile.debug` 的偵錯版本為基礎，其中包含建置它時您容器映像中的 VSDBG (.NET Core 命令列偵錯工具)。 完成 C# 模組偵錯之後，建議您針對已可供生產環境使用的 IoT Edge 模組，直接使用 `Dockerfile` 或將其自訂成不含 VSDBG。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立 IoT Edge 模組並部署它來進行偵錯。 您開始在 VS Code 中對它進行偵錯。 在 VS Code 中開發 Azure IoT Edge 時，若要了解其他案例，請參閱： 

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 中的多個模組來開發 IoT Edge 解決方案](tutorial-multiple-modules-in-vscode.md)

