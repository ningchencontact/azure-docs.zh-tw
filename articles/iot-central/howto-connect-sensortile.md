---
title: SensorTile.box 裝置連線至您的 Azure IoT Central 應用程式 |Microsoft Docs
description: 身為裝置開發人員，了解如何將 SensorTile.box 裝置連接至 Azure IoT Central 應用程式。
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 580a8baa19e8ed4fc3f4449ead9d8aedbc4c039a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160901"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>SensorTile.box 裝置連接到您的 Azure IoT Central 應用程式

這篇文章說明如何，身為裝置開發人員，將 SensorTile.box 裝置連線至 Microsoft Azure IoT Central 應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成這篇文章中的步驟，您需要下列資源：

* SensorTile.box 裝置，請參閱[SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/SensorTile.box)如需詳細資訊。
* ST BLE 感應器應用程式安裝在您的 Android 裝置，您可以 [從這裡下載] (https://play.google.com/store/apps/details?id=com.st.bluems)。 如需詳細資訊，請造訪: [ST BLE 感應器] (http://www.st.com/stblesensor)
* 從建立 Azure IoT Central 應用程式**DevKits**應用程式範本。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
* 新增**SensorTile.box**到您的 IoT Central 應用程式，請瀏覽裝置範本**裝置範本**頁面上，按一下 **+ 新增**，然後選取**SensorTile**範本。

### <a name="get-your-device-connection-details"></a>取得您的裝置連線詳細資料

Azure IoT Central 應用程式中加入實際的裝置，從**SensorTile.box**裝置範本，並記下的裝置連線詳細資料：**範圍識別碼、 裝置識別碼和主索引鍵**:

1. 新增 Device Explorer 中的 裝置。 選取 [ **+ 新增] > 實際**將實際的裝置。

    * 輸入小寫**裝置識別碼**，或使用建議**裝置識別碼**。
    * 請輸入**裝置名稱**，或使用建議的名稱

    ![新增裝置](media/howto-connect-sensortile/real-device.png)

1. 若要取得裝置連接的詳細資訊，**領域識別碼**，**裝置識別碼**，和**主索引鍵**，選取**Connect** [裝置] 頁面上。

    ![連線詳細資料](media/howto-connect-sensortile/connect-device.png)

1. 記下 連線詳細資料。 您會暫時中斷網際網路連線時準備您的 DevKit 裝置下一步。

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>設定行動應用程式與 SensorTile.box

在本節中，您將學習如何將推送到裝置的應用程式韌體和裝置將資料傳送至 IoT 中心 ST BLE 感應器行動應用程式透過藍牙低功耗 (BLE) 連線。
1. 開啟 [ST BLE 感應器應用程式，然後按**建立新的應用程式**] 按鈕。

    ![建立應用程式](media/howto-connect-sensortile/create-app.png)

1. 選取  **Barometer**應用程式。
1. 按 [上傳] 按鈕。

    ![Barometer 上傳](media/howto-connect-sensortile/barometer-upload.png)

1. 按下您 SensorTile.box 相關聯的 [播放] 按鈕。
1. 此程序完成時，SensorTile.box 會透過 BLE 串流溫度、 壓力和溼度。

## <a name="connect-the-sensortilebox-to-the-cloud"></a>連線到雲端的 SensorTile.box

在本節中，您將學習如何 SensorTile.box 連接至行動應用程式中，並連接到雲端的行動應用程式。
1. 使用左側的功能表中，選取**雲端記錄** 按鈕。

    ![雲端記錄](media/howto-connect-sensortile/cloud-logging.png)

1. 選取  **Azure IoT Central**為雲端提供者。
1. 插入的裝置識別碼和先前記下的範圍 ID。

    ![認證](media/howto-connect-sensortile/credentials.png)

1. 選取 **應用程式金鑰**選項按鈕。
1. 按一下  **Connect** ，然後選取您想要上傳的遙測資料。
1. 幾秒鐘後，資料會出現在 IoT Central 應用程式儀表板上。

## <a name="sensortilebox-device-template-details"></a>SensorTile.box 裝置範本詳細資料

建立從 SensorTile.box 裝置範本具有下列特性的應用程式：

### <a name="telemetry"></a>遙測

| 欄位名稱     | Units  | 最小值 | 最大值 | 小數位數 |
| -------------- | ------ | ------- | ------- | -------------- |
| 溼度       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 1              |
| gyroscopeZ     | dps   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |


## <a name="next-steps"></a>後續步驟

既然您已了解如何連接到您的 Azure IoT Central 應用程式的 SensorTile.box，建議的下一個步驟是了解如何[設定自訂裝置範本](howto-set-up-template.md)IoT 裝置。
