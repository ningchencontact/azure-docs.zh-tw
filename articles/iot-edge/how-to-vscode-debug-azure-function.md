---
title: 使用 Visual Studio Code 為 Azure IoT Edge 上的 Azure Functions 偵錯 | Microsoft Docs
description: 在 VS Code 中偵錯 Azure IoT Edge 上的 C# Azure Functions
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8c266a01375bf74fd4df9290255e84bc28e6089c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>使用 Visual Studio Code 為 Azure IoT Edge 上的 Azure Functions 偵錯

本文提供使用 [Visual Studio Code](https://code.visualstudio.com/) 作為主要開發工具，偵錯 IoT Edge 上 Azure Functions 的詳細指示。

## <a name="prerequisites"></a>先決條件
本文假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 IoT Edge 裝置可以是其他實體裝置，您也可以在開發機器上模擬 IoT Edge 裝置。

遵循本文中的指引之前，請完成[使用 Visual Studio Code 中的多個模組來開發 IoT Edge 解決方案](tutorial-multiple-modules-in-vscode.md)中的步驟。 在這之後，您應該已準備好下列項目：
- 在您的開發機器上執行的本機 Docker 登錄。 建議您使用本機 Docker 登錄作為原型並用於測試。 您可以在每個模組資料夾中的 `module.json` 檔案中更新容器登錄。
- 具有 Azure 函式模組子資料夾的 IoT Edge 解決方案專案工作區。
- 具有您函式程式碼的 `run.csx` 檔案。
- 在您的開發機器上執行的 Edge 執行階段。

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>建置用於偵錯的 IoT Edge 函式模組
1. 若要開始偵錯，您必須使用 **Dockerfile.amd64.debug** 重建您的 docker 映像，並再次部署 Edge 解決方案。 在 VS Code 總管中，瀏覽至 `deployment.template.json` 檔案。 透過在結尾加上 `.debug` 以更新您的功能映像 URL。

    ![建置偵錯映像](./media/how-to-debug-csharp-function/build-debug-image.png)

2. 重建您的解決方案。 在 VS Code 命令選擇區中，輸入並執行命令**Edge：建置 IoT Edge 解決方案**。

3. 在 Azure IoT 中樞裝置總管中，以滑鼠右鍵按一下 IoT Edge 裝置 ID，然後選取 [Create deployment for Edge device] \(針對 Edge 裝置建立部署\)。 選取 `config` 資料夾下的 `deployment.json`。 接著，您可以看到已順利建立部署，而且 VS Code 整合式終端機中會有部署 ID。

> [!NOTE]
> 您可以在 VS Code Docker 總管中檢查您的容器狀態，或透過在終端機中執行 `docker images` 命令來檢查。

## <a name="start-debugging-c-function-in-vs-code"></a>在 VS Code 中開始針對 C# 函式進行偵錯
1. VS Code 會將偵錯設定資訊保留在您工作區中之 `.vscode` 資料夾的 `launch.json` 檔案中。 這個 `launch.json` 檔案是在建立新 IoT Edge 解決方案時所產生。 此外，這個檔案會在您每次加入支援偵錯的新模組時更新。 瀏覽到偵錯檢視並選取對應的偵錯設定檔案。
    ![選取偵錯設定](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. 瀏覽至 `run.csx`。 在函式中新增中斷點。

3. 按一下 [開始偵錯] 按鈕或按**F5**，並選取要附加至哪個處理序。

4. 在 VS Code 偵錯檢視中，您可以在左面板中看到變數。 


> [!NOTE]
> 以上範例示範如何偵錯容器上的 .Net Core IoT Edge 函式。 它以 `Dockerfile.amd64.debug` 的偵錯版本為依據，此版本包含建立時容器映像中的 VSDBG (.NET Core 命令列偵錯工具)。 完成 C# 函式偵錯之後，我們建議您直接使用，或針對生產就緒 IoT Edge 函式自訂無 VSDBG 的 `Dockerfile`。

## <a name="next-steps"></a>後續步驟


[使用 Visual Studio Code 搭配 Azure IoT Edge 進行 C# 模組偵錯](how-to-vscode-debug-csharp-module.md)

