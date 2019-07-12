---
title: 將 Windows IoT 核心版裝置連線到 Azure IoT 中心應用程式 | Microsoft Docs
description: 如何以裝置開發人員身分，將 MXChip IoT DevKit 裝置連線到 Azure IoT 中心應用程式。
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e8d4ab46c598580a3a87f4344202f2700926bf5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510330"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>將 Windows IoT 核心版裝置連線到 Azure IoT 中心應用程式

本文說明如何以裝置開發人員身分，將 Windows IoT 核心版裝置連線到 Microsoft Azure IoT 中心應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成這篇文章中的步驟，您需要下列項目︰

- Azure IoT Central 應用程式是從**範例 Devkits** 應用程式範本建立而來。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。

- 執行 Windows 10 IoT 核心版作業系統的裝置。 如需詳細資訊，請參閱 <<c0> [ 設定您的 Windows 10 IoT 核心版裝置](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)。

- 使用在開發電腦[Node.js](https://nodejs.org/) 8.0.0 版或更新版本安裝。 您可以在命令列執行 `node --version` 來檢查版本。 Node.js 適用於多種作業系統。

## <a name="the-sample-devkits-application"></a>範例 Devkits 應用程式

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Windows IoT 核心版**裝置範本：

- 裝置的遙測度量：**溼度**，**溫度**，以及**壓力**。
- 設定來控制**風扇速度**。
- 裝置屬性**骰子數字**而定域機組屬性**位置**。

完整的裝置範本設定的詳細資訊，請參閱 < [Windows IoT Core 裝置範本詳細資料](#device-template-details)。

## <a name="add-a-real-device"></a>新增真實裝置

Azure IoT Central 應用程式中使用**Device Explorer**頁面，即可加入實際的裝置，從**Windows 10 IoT 核心版**裝置範本。 請記下的裝置的連線詳細資料 (**領域識別碼**，**裝置識別碼**，並**主索引鍵**)。 如需詳細資訊，請參閱 <<c0> [ 取得連線資訊](howto-generate-connection-string.md#get-connection-information)。

## <a name="prepare-the-device"></a>準備裝置

裝置連線至 IoT Central，它需要的連接字串。

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

若要存取的連接字串的裝置程式碼，將它儲存在名為的檔案**connection.string.iothub**資料夾中`C:\Data\Users\DefaultAccount\Documents\`Windows 10 IoT 核心版裝置上。

若要複製**connection.string.iothub**檔案從桌上型電腦`C:\Data\Users\DefaultAccount\Documents\`在裝置上的資料夾，您可以使用[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. 您可以使用網頁瀏覽器來瀏覽至您的裝置上 Windows Device Portal。
1. 若要瀏覽您的裝置上的檔案，請選擇**應用程式 > 檔案總管**。
1. 瀏覽至**使用者 Folders\Documents**。 然後上傳**connection.string.iothub**檔案：

    ![上傳連接字串](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>部署並執行

若要部署，並在裝置上執行範例應用程式，您可以使用[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. 您可以使用網頁瀏覽器來瀏覽至您的裝置上 Windows Device Portal。
1. 若要部署和執行**Azure IoT 中樞用戶端**應用程式中，選擇**應用程式 > 快速執行範例**。 然後選擇**Azure IoT 中樞用戶端**。
1. 然後選擇**部署和執行**。

    ![部署並執行](media/howto-connect-windowsiotcore/quick-run.png)

在幾分鐘的時間之後, 您可以從您的裝置檢視遙測 IoT Central 應用程式中。

[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)內含的工具，可用來疑難排解您的裝置：

- **Apps manager**頁面可讓您控制裝置上執行的應用程式。
- 如果您不需要監視連線到您的裝置，您可以使用**裝置設定**頁面，即可擷取螢幕擷取畫面，從您的裝置。 例如:

    ![應用程式螢幕擷取畫面](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>下載原始程式碼

如果您想要探索和修改用戶端應用程式的原始程式碼，您可以下載從[Windows iotcore 範例 GitHub 儲存機制](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients)。

## <a name="device-template-details"></a>裝置範本詳細資料

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Windows IoT 核心版**裝置範本：

### <a name="telemetry-measurements"></a>遙測量測

| 欄位名稱     | Units  | 最小值 | 最大值 | 小數位數 |
| -------------- | ------ | ------- | ------- | -------------- |
| 溼度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>設定

數值設定

| Display name | 欄位名稱 | Units | 小數位數 | 最小值 | 最大值 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 風扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>屬性

| type            | Display name | 欄位名稱 | 資料類型 |
| --------------- | ------------ | ---------- | --------- |
| 裝置屬性 | 模具編號   | dieNumber  | number    |
| Text            | Location     | location   | N/A       |

## <a name="next-steps"></a>後續步驟

既然您已了解如何將 Windows IoT 核心版裝置連接至 Azure IoT Central 應用程式，建議的下一個步驟是了解如何[設定自訂裝置範本](howto-set-up-template.md)IoT 裝置。
