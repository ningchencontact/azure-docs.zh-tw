---
title: 將 SensorTile 裝置連線到您的 Azure IoT Central 應用程式 |Microsoft Docs
description: 身為裝置開發人員，請瞭解如何將 SensorTile 裝置連線到您的 Azure IoT Central 應用程式。
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 0969ee6dbc035ffa105dd54d34f3b4711d4915cf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951207"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>將 SensorTile 裝置連線到您的 Azure IoT Central 應用程式

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本文說明如何以裝置開發人員身分，將 SensorTile 裝置連線到您的 Microsoft Azure IoT Central 應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成本文中的步驟，您需要下列資源：

* SensorTile 的裝置。 如需詳細資訊，請參閱[SensorTile](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html)。
* 在您的 Android 裝置上安裝 ST BLE 感應器應用程式，您可以[從這裡下載](https://play.google.com/store/apps/details?id=com.st.bluems)。 如需詳細資訊，請造訪： [ST BLE 感應器](https://www.st.com/stblesensor)
* 從**DevKits**應用程式範本建立的 Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
* 藉由造訪 [**裝置範本**] 頁面，按一下 [ **+ 新增**]，然後選取 [ **SensorTile** ] 範本，將 [ **SensorTile** ] 裝置範本新增至您的 IoT Central 應用程式。

### <a name="get-your-device-connection-details"></a>取得您的裝置連線詳細資料

在您的 Azure IoT Central 應用程式中，從**SensorTile**裝置範本新增真實裝置，並記下裝置連線詳細資料： [**範圍識別碼**]、[**裝置識別碼**] 和 [**主要金鑰**]：

1. 從裝置新增裝置。 選取 [ **+ 新增] [> real** ] 以新增實際裝置。

    * 請輸入小寫的**裝置識別碼**，或使用建議的**裝置識別碼**。
    * 輸入**裝置名稱**，或使用建議的名稱

    ![新增裝置](media/howto-connect-sensortile/real-device.png)

1. 若要取得裝置連線詳細資料、**範圍識別碼**、**裝置識別碼**和**主要金鑰**，請選取 [裝置] 頁面上的 **[連線]** 。

    ![連線詳細資料](media/howto-connect-sensortile/connect-device.png)

1. 記下連接詳細資料。 當您在下一個步驟中準備 DevKit 裝置時，會暫時中斷與網際網路的連線。

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>使用行動應用程式設定 SensorTile

在本節中，您將瞭解如何將應用程式固件推送至裝置。 接著，您可以使用 Bluetooth 低功耗（BLE）連線，透過 ST BLE 感應器行動應用程式，將裝置資料傳送至 IoT Central。

1. 開啟 ST BLE 感應器應用程式，然後按 [**建立新的應用程式**] 按鈕。

    ![建立應用程式](media/howto-connect-sensortile/create-app.png)

1. 選取 [**氣壓計**] 應用程式。
1. 按下 [上傳] 按鈕。

    ![氣壓計上傳](media/howto-connect-sensortile/barometer-upload.png)

1. 按下與 SensorTile 相關聯的 [播放] 按鈕。
1. 當程式完成時，SensorTile 會串流處理溫度、壓力和濕度超過 BLE。

## <a name="connect-the-sensortilebox-to-the-cloud"></a>將 [SensorTile] 連接到雲端

在本節中，您將瞭解如何將 SensorTile 連接至行動應用程式，並將行動應用程式連線到雲端。

1. 使用左窗格，選取 [**雲端記錄**] 按鈕。

    ![雲端記錄](media/howto-connect-sensortile/cloud-logging.png)

1. 選取 [ **Azure IoT Central** ] 做為雲端提供者。
1. 插入先前記下的裝置識別碼和範圍識別碼。

    ![認證](media/howto-connect-sensortile/credentials.png)

1. 選取 [**應用程式金鑰**] 選項按鈕。
1. 按一下 **[連線]** ，然後選取您想要上傳的遙測資料。
1. 幾秒後，資料就會出現在 IoT Central 應用程式儀表板上。

## <a name="sensortilebox-device-template-details"></a>SensorTile 裝置範本詳細資料

從 SensorTile 裝置範本建立的應用程式，具有下列特性：

### <a name="telemetry"></a>遙測

| 欄位名稱     | 單位數  | 最小值 | 最大值 | 小數位數 |
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

既然您已瞭解如何將 SensorTile 連線到您的 Azure IoT Central 應用程式，建議的下一個步驟是瞭解如何為您自己的 IoT 裝置[設定自訂裝置範本](howto-set-up-template.md)。
