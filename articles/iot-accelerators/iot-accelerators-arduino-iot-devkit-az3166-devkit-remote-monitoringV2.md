---
title: IoT DevKit 到雲端-- 將 IoT DevKit AZ3166 連線到遠端監視 IoT 解決方案加速器 | Microsoft Docs
description: 在本教學課程中，了解如何將 IoT DevKit AZ3166 上的感應器狀態傳送至遠端監視 IoT 解決方案加速器來進行監視和呈現視覺效果。
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 32742b2a680370f443051e2d86f90d94e8632850
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720577"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>將 MXChip IoT DevKit AZ3166 連線到 IoT 遠端監視解決方案加速器

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

您將會了解如何在 IoT DevKit 上執行範例應用程式，以將感應器資料傳送至解決方案加速器。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是全方位 Arduino 相容板，具有豐富的週邊設備和感應器。 您可以使用 Visual Studio Code 中的 [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) 來對它進行開發。 它隨附不斷增長的[專案目錄](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，以引導您設定運用 Microsoft Azure 服務的物聯網 (IoT) 解決方案原型。

## <a name="what-you-need"></a>您需要什麼

請參閱[使用者入門指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)並**完成下列章節**：

* 準備硬體
* 設定 Wi-Fi
* 開始使用 DevKit
* 準備開發環境

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>在 VS Code 中開啟遠端監視範例

1. 確定 IoT DevKit **未**連線至您的電腦。 先啟動 VS Code，然後將 DevKit 連線至您的電腦。

2. 按一下 `F1` 開啟命令選擇區，輸入並選取 [IoT Workbench: 範例]。 然後選取 [IoT DevKit] 作為面板。

3. 找出 [遠端監視]，並按一下 [開啟範例]。 這會開啟一個內含專案資料夾的新 VS Code 視窗。
  ![IoT Workbench，選取 ](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)[遠端監視範例]

## <a name="configure-iot-hub-device-connection-string"></a>設定 IoT 中樞裝置連接字串

1. 將 IoT DevKit 切換為**設定模式**。 若要這樣做：
   * 按住 **A** 按鈕。
   * 按下再放開 [重設] 按鈕。

2. 畫面會顯示 DevKit 識別碼和 [設定]。
   
  ![IoT DevKit 設定模式](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. 按一下 `F1` 開啟命令選擇區，輸入並選取 [IoT Workbench：裝置 > 配置裝置設定]。

4. 貼上您剛才複製的連接字串，並按一下 `Enter` 進行設定。

## <a name="build-and-upload-the-device-code"></a>組建並上傳裝置程式碼

1. 按一下 `F1` 開啟命令選擇區，輸入並選取 [IoT Workbench：裝置 > 裝置上傳]。
  ![IoT Workbench：裝置 - > 上傳](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code 接著會開始程式碼，並上傳到您的 DevKit。
  ![IoT Workbench：裝置 - > 已上傳](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

DevKit 會重新開機，然後開始執行程式碼。

## <a name="test-the-project"></a>測試專案

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>檢視傳送至遠端監視解決方案的遙測資料

當範例應用程式執行時，DevKit 會透過 Wi-Fi 將感應器資料傳送至遠端監視解決方案。 若要查看結果，請遵循下列步驟：

1. 移至解決方案儀表板，然後按一下 [裝置]。

2. 按一下裝置名稱，在右邊索引標籤上，您可以即時看到 DevKit 的感應器狀態。
  ![Azure IoT 套件中的感應器資料](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>傳送 C2D 訊息

遠端監視解決方案可讓您在裝置上叫用遠端方法。 選取感應器時，可以在 [方法] 區段中看到範例程式碼發佈的三種方法。

![IoT DevKit 方法](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

讓我們嘗試使用「LedColor」方法變更其中一個 DevKit LED 的色彩。

1. 從裝置清單中選取裝置名稱，並按一下 [作業]。

  ![建立作業](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. 如下所示設定作業，並按一下 [套用]。
  * 選取作業：**執行方法**
  * 方法名稱：**LedColor**
  * 作業名稱：**ChangeLedColor**
  
  ![工作設定](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

在幾秒鐘內，您的 DevKit 應會變更 RGB LED 的色彩 (A 按鈕下方)。

![IoT DevKit 紅色 LED](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行教學課程，請將已部署的遠端監視解決方案加速器保留下來。

如果您不再需要解決方案加速器，可選取該加速器，然後按一下 [刪除解決方案]，從已佈建的解決方案頁面中加以刪除：

![刪除解決方案](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>問題與意見反應

如果發生問題，請參閱 [IoT DevKit 常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或使用下列管道與我們連絡：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>後續步驟

您已了解如何將 DevKit 裝置連線至 Azure IoT 遠端監視解決方案加速器並將感應器資料視覺化，以下是建議的後續步驟：

* [Azure IoT 解決方案加速器概觀](https://docs.microsoft.com/azure/iot-suite/)
* [自訂 UI](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [將 IoT DevKit 連線到 Azure IoT Central 應用程式](../iot-central/howto-connect-devkit.md)