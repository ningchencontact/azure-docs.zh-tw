---
title: 使用 Azure IoT Edge 來進行 C# 模組偵錯 | Microsoft Docs
description: 在 Visual Studio Code 中透過 Azure IoT Edge 使用 Visual Studio Code 來進行 C# 模組偵錯。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 035cb129bc3933c10f430b593226108f5d160972
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165718"
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>使用 Visual Studio Code 搭配 Azure IoT Edge 進行 C# 模組偵錯
本文提供詳細的指示，說明如何使用 [Visual Studio (VS) Code](https://code.visualstudio.com/) 作為主要開發工具來進行 Azure IoT Edge 模組偵錯。

## <a name="prerequisites"></a>先決條件
本文假設您使用執行 Windows 或 Linux 的電腦或虛擬機器作為開發電腦。 您的 IoT Edge 裝置可以是其他實體裝置，或是您也可以在開發機器上模擬 IoT Edge 裝置。

> [!NOTE]
> 您只能在 linux-amd64 容器中進行 C# 模組偵錯。

遵循本文中的指引之前，請完成[使用 Visual Studio Code 中的多個模組來開發 IoT Edge 解決方案](tutorial-multiple-modules-in-vscode.md)中的步驟。 在這之後，您應該已準備好下列項目：
- 在您的開發機器上執行的本機 Docker 登錄。 建議您使用本機 Docker 登錄作為原型並用於測試。 您可以在每個模組資料夾中的 `module.json` 檔案中更新容器登錄。
- 具有 C# 模組子資料夾的 IoT Edge 解決方案專案工作區。
- 包含最新模組程式碼的 `Program.cs` 檔案。
- 在您的開發機器上執行的 Edge 執行階段。

## <a name="build-your-iot-edge-c-module-for-debugging"></a>建置用於偵錯的 IoT Edge C# 模組
1. 若要開始偵錯，您必須使用 **Dockerfile.amd64.debug** 重建您的 docker 映像，並再次部署 Edge 解決方案。 在 VS Code 總管中，瀏覽至 `deployment.template.json` 檔案。 透過在結尾加上 `.debug` 以更新您的功能映像 URL。

2. 重建您的解決方案。 在 VS Code 命令選擇區中，輸入並執行命令**Edge：建置 IoT Edge 解決方案**。

3. 在 Azure IoT 中樞裝置總管中，以滑鼠右鍵按一下 IoT Edge 裝置識別碼，然後選取 [Create deployment for Edge device] \(針對 Edge 裝置建立部署\)。 選取 `config` 資料夾中的 `deployment.json` 檔案。 接著，您可以看到已順利建立部署，而且 VS Code 整合式終端機中會有部署識別碼。

您可以在 VS Code Docker 總管中檢查您的容器狀態，或透過在終端機中執行 `docker images` 命令來檢查。

## <a name="start-debugging-c-module-in-vs-code"></a>開始在 VS Code 中針對 C# 模組進行偵錯
1. VS Code 會將偵錯設定資訊保留在您工作區中之 `.vscode` 資料夾的 `launch.json` 檔案中。 這個 `launch.json` 檔案在您建立新 IoT Edge 解決方案時產生。 每當您新增支援偵錯的模組時，此檔案都會更新。 瀏覽到偵錯檢視並選取對應的偵錯設定檔案。
    ![選取偵錯設定](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. 瀏覽至 `program.cs`。 在此檔案中新增中斷點。

3. 按一下 [開始偵錯] 按鈕或按 **F5**，然後選取要附加至的處理序。

4. 在 VS Code 偵錯檢視中，您可以在左面板中看到變數。 

> [!NOTE]
> 上述範例說明如何針對容器上的 .Net Core IoT Edge 模組進行偵錯。 它是以 `Dockerfile.debug` 的偵錯版本為基礎，其中包含建置它時您容器映像中的 VSDBG (.NET Core 命令列偵錯工具)。 完成 C# 模組偵錯之後，建議您針對已可供生產環境使用的 IoT Edge 模組，直接使用 `Dockerfile` 或將其自訂成不含 VSDBG。

## <a name="next-steps"></a>後續步驟

[使用 Visual Studio Code 以 Azure IoT Edge 為 Azure Functions 偵錯](how-to-vscode-debug-azure-function.md)

