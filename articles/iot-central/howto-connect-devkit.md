---
title: 將 DevKit 裝置連線到 Azure IoT 中心應用程式 | Microsoft Docs
description: 如何以裝置開發人員身分，將 MXChip IoT DevKit 裝置連線到 Azure IoT 中心應用程式。
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 44af0ccab45f1335d9dfec06287303a34391eded
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113192"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>將 MXChip IoT DevKit 裝置連線到您的 Azure IoT Central 應用程式

本文說明如何以裝置開發人員身分，將 MXChip IoT DevKit (DevKit) 裝置連線到 Microsoft Azure IoT 中心應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成這篇文章中的步驟，您需要下列項目︰

1. Azure IoT Central 應用程式是從**範例 Devkits** 應用程式範本建立而來。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
1. DevKit 裝置。 若要購買 DevKit 裝置，請造訪 [MXChip IoT DevKit](http://mxchip.com/az3166)。

## <a name="sample-devkits-application"></a>範例 Devkits 應用程式

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **MXChip** 裝置範本：

- 包含裝置**溼度**、**溫度**、**壓力**、**磁強計** (沿著 X、Y、Z 軸測量)、**加速計** (沿著 X、Y、Z 軸測量) 和**陀螺儀** (沿著 X、Y、Z 軸測量) 等測量值的遙測。
- 包含**裝置狀態**範例度量的狀態。
- 包含 **按下按鈕 B** 事件的事件度量。 
- 顯示**電壓**、**電流**、**風扇速度**和 **IR** 切換等設定。
- 屬性包含裝置屬性**印模編號**和**裝置位置**，這是位置屬性，也可在**製造地點**雲端屬性中找到。 

如需組態的完整詳細資料，請參閱 [MXChip 裝置範本詳細資料](#mxchip-device-template-details)


## <a name="add-a-real-device"></a>新增真實裝置

在 Azure IoT Central 應用程式中，從 **MXChip** 裝置範本新增真實裝置，並記下裝置連線詳細資料 ([範圍識別碼]、[裝置識別碼] 和 [主要金鑰])。

1. 新增**真實裝置**從 [Device Explorer 中，選取 **+ 新增] > 實際**將實際的裝置。

   * 輸入裝置識別碼**<span style="color:Red"> (應為小寫)</span>**，或使用建議的裝置識別碼。
   * 輸入裝置名稱，或使用建議的名稱

     ![新增裝置](media/howto-connect-devkit/add-device.png)

1. 取得連線詳細資料，例如**領域識別碼、 裝置識別碼和主要金鑰**新增的裝置，選取**Connect** [裝置] 頁面上。

    ![連線詳細資料](media/howto-connect-devkit/device-connect.png)

1. 請務必儲存這些詳細資料，因為在準備 DevKit 裝置時會暫時中斷與網際網路的連線。

### <a name="prepare-the-devkit-device"></a>準備 DevKit 裝置

> [!NOTE]
> 如果您先前已經使用裝置並且已儲存 WiFi 認證，但是想要將裝置重新設定為使用不同的 WiFi 網路、連接字串或遙測度量，請同時按面板上的 **A** 和 **B** 按鈕。 如果沒有作用，請按 [重設] 按鈕，然後再試一次。

#### <a name="to-prepare-the-devkit-device"></a>準備 DevKit 裝置

1. 從 GitHub 上的[版本](https://aka.ms/iotcentral-docs-MXChip-releases)頁面下載適用於 MXChip 的最新預先建置 Azure IoT 中心韌體。
1. 使用 USB 纜線將 DevKit 裝置連接到開發電腦。 在 Windows 中，檔案總管視窗會在對應到 DevKit 裝置上儲存體的磁碟機上開啟。 例如，此磁碟機可能會稱為 **AZ3166 (D:)**。
1. 將 **iotCentral.bin** 檔案拖曳到磁碟機視窗。 複製完成時，使用新的韌體重新啟動裝置。

1. 當 DevKit 裝置重新啟動時，會顯示下列畫面：

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 如果畫面顯示任何其他項目，請重設裝置，並同時按下裝置上的 **A** 和 **B** 按鈕重新啟動裝置。

1. 裝置現在處於存取點 (AP) 模式。 您可以從您的電腦或行動裝置連線到此 WiFi 存取點。

1. 在您的電腦、電話或平板電腦上，連線到裝置畫面上顯示的 WiFi 網路名稱。 當您連線到此網路時，您無法存取網際網路。 此狀態在預料之內，而您只會在設定裝置時連線到此網路短暫時間。

1. 開啟網頁瀏覽器並巡覽至 [http://192.168.0.1/start](http://192.168.0.1/start)。 以下網頁隨即顯示：

    ![裝置設定頁面](media/howto-connect-devkit/configpage.png)

    在網頁中： 
    - 新增 WiFi 網路的名稱 
    - WiFi 網路密碼
    - 裝置 LCD 上顯示的 PIN CODE 
    - 裝置的連線詳細資料 [範圍識別碼]、[裝置識別碼] 和 [主要金鑰] (您應該已遵循步驟儲存這項資訊)      
    - 選取所有可用的遙測量值！ 

1. 選擇 [設定裝置] 之後，您會看到這個頁面：

    ![已設定裝置](media/howto-connect-devkit/deviceconfigured.png)

1. 按裝置上的 [重設] 按鈕。


## <a name="view-the-telemetry"></a>檢視遙測資料

當 DevKit 裝置重新啟動時，裝置上的畫面會顯示：

* 傳送的遙測訊息數目。
* 失敗次數。
* 接收的所需屬性數目和傳送的報告屬性數目。

> [!NOTE]
> 如果裝置似乎在連線檢查期間形成迴圈，請在 IoT Central 中已「封鎖」裝置時，「解除鎖定」該裝置，讓它可以連線到應用程式。

搖動裝置，使傳送的報告屬性數目遞增。 裝置會傳送一個隨機數字作為 [Die 數字] 裝置屬性。

您可以檢視遙測量值和報告屬性值，並且在 Azure IoT 中心進行設定：

1. 使用 [裝置總管]，瀏覽至您所新增真實 MXChip 裝置的 [量值] 頁面：

    ![瀏覽至真實裝置](media/howto-connect-devkit/realdevicenew.png)

1. 在 [量值] 頁面上，您可以看到來自 MXChip 裝置的遙測：

    ![從真實裝置檢視遙測](media/howto-connect-devkit/devicetelemetrynew.png)

1. 在 [屬性] 頁面上，您可以檢視裝置所報告的最後一個印模號碼和裝置位置：

    ![檢視裝置屬性](media/howto-connect-devkit/devicepropertynew.png)

1. 在 [設定] 頁面上，您可以在 MXChip 裝置上更新設定：

    ![檢視裝置設定](media/howto-connect-devkit/devicesettingsnew.png)

1. 在 [儀表板] 頁面上，您可以看到位置地圖

    ![檢視裝置儀表板](media/howto-connect-devkit/devicedashboardnew.png)


## <a name="download-the-source-code"></a>下載原始程式碼

如果您想要探索並修改裝置程式碼，您可以從 GitHub 下載它。 如果您打算修改程式碼，您應該遵循下列指示，為您的桌上型電腦作業系統[準備開發環境](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)。

若要下載原始程式碼，請在桌上型電腦上執行下列命令：

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

前一個命令會將原始程式碼下載到名為 `iot-central-firmware` 的資料夾。 

> [!NOTE]
> 如果您的開發環境中未安裝 **git**，您可以從 [https://git-scm.com/download](https://git-scm.com/download) 下載它。

## <a name="review-the-code"></a>檢閱程式碼

使用當您準備開發環境時安裝的 Visual Studio Code，以開啟 `iot-central-firmware` 資料夾中的 `AZ3166` 資料夾： 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

若要查看遙測如何傳送至 Azure IoT 中心應用程式，請開啟來源資料夾中的 **main_telemetry.cpp** 檔案。

此函式 `buildTelemetryPayload` 會使用裝置上感應器的資料，建立 JSON 遙測承載。

此函式 `sendTelemetryPayload` 會呼叫 **iotHubClient.cpp** 中的 `sendTelemetry`，將 JSON 承載傳送到 Azure IoT 中心應用程式使用的 IoT 中樞。

若要查看如何向 Azure IoT 中心應用程式報告屬性值，請開啟來源資料夾中的 **main_telemetry.cpp** 檔案。

此函式 `telemetryLoop` 會在加速計偵測到點兩下時傳送 **doubleTap** 報告屬性。 它會使用 **iotHubClient.cpp** 來源檔案中的 `sendReportedProperty` 函式。

**iotHubClient.cpp** 來源檔案中的程式碼會使用[適用於 C 的 Microsoft Azure IoT SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-c)中的函式，來與 IoT 中樞互動。

如需如何修改、建置範例程式碼以及將其上傳到裝置的相關資訊，請參閱 `AZ3166` 資料夾中的 **readme.md** 檔案。

## <a name="mxchip-device-template-details"></a>MXChip 裝置範本詳細資料

從範例 Devkits 應用程式範本建立的應用程式包含具有下列特性的 MXChip 裝置範本：

### <a name="measurements"></a>量測

#### <a name="telemetry"></a>遙測 

| 欄位名稱     | Units  | 最小值 | 最大值 | 小數位數 |
| -------------- | ------ | ------- | ------- | -------------- |
| 溼度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |


#### <a name="states"></a>狀態 
| 名稱          | 顯示名稱   | 正常 | 警告 | 危險 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | 裝置狀態   | 綠色  | 橙色  | 紅色    | 

#### <a name="events"></a>活動 
| 名稱             | 顯示名稱      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | 按下按鈕 B  | 

### <a name="settings"></a>設定

數值設定

| 顯示名稱 | 欄位名稱 | Units | 小數位數 | 最小值 | 最大值 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 電壓      | setVoltage | 伏特 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 安培  | 0              | 0       | 100     | 0       |
| 風扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

切換設定

| 顯示名稱 | 欄位名稱 | 開啟文字 | 關閉文字 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 開啟      | 關      | 關閉     |

### <a name="properties"></a>properties

| 類型            | 顯示名稱 | 欄位名稱 | 資料類型 |
| --------------- | ------------ | ---------- | --------- |
| 裝置屬性 | 模具編號   | dieNumber  | number    |
| 裝置屬性 | 裝置位置   | location  | location    |
| 文字            | 製造地     | 製造地   | N/A       |



## <a name="next-steps"></a>後續步驟

您現在已了解如何將 DevKit 裝置連線至 Azure IoT 中心應用程式，以下是建議的後續步驟：

* [準備及連線 Raspberry Pi](howto-connect-raspberry-pi-python.md)
