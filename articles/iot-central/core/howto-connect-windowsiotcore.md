---
title: 將 Windows IoT 核心版裝置連線到 Azure IoT 中心應用程式 | Microsoft Docs
description: 如何以裝置開發人員身分，將 MXChip IoT DevKit 裝置連線到 Azure IoT 中心應用程式。
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d880130e114b2bd3e4f978c2ae3fc7bacf0648c4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953898"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>將 Windows IoT 核心版裝置連線到 Azure IoT 中心應用程式

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本文說明如何以裝置開發人員身分，將 Windows IoT 核心版裝置連線到 Microsoft Azure IoT 中心應用程式。

## <a name="before-you-begin"></a>開始之前

若要完成這篇文章中的步驟，您需要下列項目︰

- Azure IoT 中心應用程式是從**範例 Devkits** 應用程式範本建立而來。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。

- 執行 Windows 10 IoT 核心版作業系統的裝置。 如需詳細資訊，請參閱[設定您的 Windows 10 IoT 核心版裝置](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)。

- 已[安裝 node.js 8.0.0 版或](https://nodejs.org/)更新版本的開發電腦。 您可以在命令列執行 `node --version` 來檢查版本。 Node.js 適用於多種作業系統。

## <a name="the-sample-devkits-application"></a>範例 Devkits 應用程式

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Windows IoT 核心版**裝置範本：

- 裝置的遙測測量：**濕度**、**溫度**和**壓力**。
- 設定以控制**風扇速度**。
- 裝置屬性的**骰子編號**和雲端屬性**位置**。

如需裝置範本設定的完整詳細資訊，請參閱[Windows IoT 核心版裝置範本詳細資料](#device-template-details)。

## <a name="add-a-real-device"></a>新增實際裝置

在您的 Azure IoT Central 應用程式中，使用 [ **Device Explorer** ] 頁面，從**Windows 10 IoT 核心版**裝置範本新增實際裝置。 記下裝置連線詳細資料（**範圍識別碼**、**裝置識別碼**和**主要金鑰**）。

## <a name="prepare-the-device"></a>準備裝置

若要讓裝置連接到 IoT Central，它需要連接字串：

1. 使用 `dps-keygen` 命令列公用程式產生連接字串：

    若要安裝 [金鑰產生器公用程式](https://github.com/Azure/dps-keygen)，請執行下列命令：

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 若要產生連接字串，請使用您先前記下的連線詳細資料執行下列命令：

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. 從 `dps-keygen` 輸出中複製連接字串，在您的裝置程式碼中使用。

若要讓裝置程式碼存取連接字串，請將它儲存在 Windows 10 IoT 核心版裝置上 `C:\Data\Users\DefaultAccount\Documents\` 資料夾中名為**iothub**的檔案中。

若要將**iothub**檔案從您的桌上型電腦複製到裝置上的 `C:\Data\Users\DefaultAccount\Documents\` 資料夾，您可以使用[Windows 裝置入口網站](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)：

1. 使用您的網頁瀏覽器流覽至您裝置上的 Windows 裝置入口網站。
1. 若要流覽裝置上的檔案，請選擇 [**應用程式] > [檔案瀏覽器**]。
1. 流覽至 [**使用者 Folders\Documents**]。 然後上傳**iothub**檔案：

    ![上傳連接字串](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>部署並執行

若要在您的裝置上部署並執行範例應用程式，您可以使用[Windows 裝置入口網站](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)：

1. 使用您的網頁瀏覽器流覽至您裝置上的 Windows 裝置入口網站。
1. 若要部署和執行**Azure IoT 中樞用戶端**應用程式，請選擇 [**應用程式 > 快速執行範例**]。 然後選擇 [ **Azure IoT 中樞用戶端**]。
1. 然後選擇 [**部署並執行**]。

    ![部署並執行](media/howto-connect-windowsiotcore/quick-run.png)

幾分鐘後，您就可以在 IoT Central 應用程式中查看裝置的遙測資料。

[Windows 裝置入口網站](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)包含的工具可讓您用來針對裝置進行疑難排解：

- [**應用程式管理員**] 頁面可讓您控制在裝置上執行的應用程式。
- 如果您的裝置沒有連線的監視器，您可以使用 [**裝置設定**] 頁面，從您的裝置中抓取螢幕擷取畫面。 例如：

    ![應用程式螢幕擷取畫面](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>下載原始程式碼

如果您想要探索並修改用戶端應用程式的原始程式碼，您可以從[Windows iotcore 範例 GitHub 存放庫](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients)下載。

## <a name="device-template-details"></a>裝置範本詳細資料

從**範例 Devkits** 應用程式範本建立的應用程式包含具有下列特性的 **Windows IoT 核心版**裝置範本：

### <a name="telemetry-measurements"></a>遙測量值

| 欄位名稱     | 單位數  | 最小值 | 最大值 | 小數位數 |
| -------------- | ------ | ------- | ------- | -------------- |
| 溼度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>設定

數值設定

| 顯示名稱 | 欄位名稱 | 單位數 | 小數位數 | 最小值 | 最大值 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 風扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>屬性

| Type            | 顯示名稱 | 欄位名稱 | Data type |
| --------------- | ------------ | ---------- | --------- |
| 裝置屬性 | 模具編號   | dieNumber  | number    |
| 文字            | Location     | location   | N/A       |

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何將 Windows IoT 核心版裝置連線到您的 Azure IoT Central 應用程式，建議的下一個步驟是瞭解如何為您自己的 IoT 裝置[設定自訂裝置範本](howto-set-up-template.md)。
