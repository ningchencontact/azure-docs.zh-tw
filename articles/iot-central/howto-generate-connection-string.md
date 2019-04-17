---
title: 產生的裝置連接字串的 Azure IoT Central |Microsoft Docs
description: 身為裝置開發人員，要如何產生連接字串需要連線到 IoT Central 應用程式的裝置？
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f302cbfa7152ae30be434f560c0c39056d40f9f4
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615756"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>產生裝置連接字串來連接到 Azure IoT Central 應用程式

這篇文章說明如何，身為裝置開發人員，以產生需要連線到 IoT Central 應用程式的裝置連接字串。 這篇文章中所述的程序會示範如何快速地連接單一裝置，使用共用的存取簽章 (SAS)。 當您利用 IoT Central 試用或測試的裝置，則這個方法會很有用。 若要在生產環境中使用的替代方法，請參閱 <<c0> [ 裝置的連線，在 Azure IoT Central](concepts-connectivity.md)。

## <a name="prerequisites"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目︰

- Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
- 使用在開發電腦[Node.js](https://nodejs.org/) 8.0.0 版或更新版本安裝。 您可以在命令列執行 `node --version` 來檢查版本。 Node.js 適用於多種作業系統。

## <a name="get-connection-information"></a>取得連線資訊

下列步驟說明如何取得您需要產生裝置的 SAS 連接字串的資訊：

1. 在  **Device Explorer**，尋找您想要連接到您的應用程式的真實裝置：

    ![選取實際的裝置](media/howto-generate-connection-string/real-devices.png)

1. 在 **裝置**頁面上，選取**Connect**:

    ![選取連線](media/howto-generate-connection-string/connect.png)

1. 請記下的連線詳細資料，**領域識別碼**，**裝置識別碼**，並**裝置主索引鍵**，若要使用下列步驟：

    ![連線詳細資料](media/howto-generate-connection-string/device-connect.png)

    您可以複製此頁面以儲存的值。

## <a name="generate-the-connection-string"></a>產生連接字串

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>後續步驟

既然您已產生實際的裝置連接到您的 Azure IoT Central 應用程式的連接字串，以下是建議的後續步驟：

* [準備及連線 DevKit 裝置 (C)](howto-connect-devkit.md)
* [準備及連線 Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [準備及連線 Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [準備及連線 Windows 10 IoT 核心裝置 (C#)](howto-connect-windowsiotcore.md)
* [將一般 Node.js 用戶端連線至 Azure IoT Central 應用程式](howto-connect-nodejs.md)