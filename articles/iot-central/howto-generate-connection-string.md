---
title: 產生 Azure IoT Central 的裝置連接字串 |Microsoft Docs
description: 身為裝置開發人員，如何為需要連接到 IoT Central 應用程式的裝置產生連接字串？
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019785"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>產生裝置連接字串以連接至 Azure IoT Central 應用程式

本文說明如何以裝置開發人員的身分，為需要連接到 IoT Central 應用程式的裝置產生連接字串。 本文中所述的程式說明如何使用共用存取簽章（SAS）快速連接單一裝置。 當您要試驗 IoT Central 或測試裝置時，這個方法很有用。 如需在實際執行環境中使用的替代方法，請參閱[Azure IoT Central 中的裝置連線能力](concepts-connectivity.md)。

## <a name="prerequisites"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目︰

- Azure IoT Central 應用程式。 如需詳細資訊，請參閱[建立應用程式快速入門](quick-deploy-iot-central.md)。
- 已[安裝 node.js 8.0.0 版或](https://nodejs.org/)更新版本的開發電腦。 您可以在命令列執行 `node --version` 來檢查版本。 Node.js 適用於多種作業系統。

## <a name="get-connection-information"></a>取得連線資訊

下列步驟說明如何取得為裝置產生 SAS 連接字串所需的資訊：

1. 在  **Device Explorer**中，尋找您想要連接到應用程式的實際裝置：

    ![選取實際裝置](media/howto-generate-connection-string/real-devices.png)

1. 在 [**裝置**] 頁面上，選取 **[連線]** ：

    ![選取 [連線]](media/howto-generate-connection-string/connect.png)

1. 記下 [連線詳細資料]、[**範圍識別碼**]、[**裝置識別碼**] 和 [**裝置主要金鑰**]，以在下列步驟中使用：

    ![連線詳細資料](media/howto-generate-connection-string/device-connect.png)

    您可以從這個頁面複製值以儲存。

## <a name="generate-the-connection-string"></a>產生連接字串

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>後續步驟

既然您已為實際裝置產生連接字串，以連接到您的 Azure IoT Central 應用程式，以下是建議的後續步驟：

* [準備及連線 DevKit 裝置 (C)](howto-connect-devkit.md)
* [準備及連線 Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [準備及連線 Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [準備及連線 Windows 10 IoT 核心裝置 (C#)](howto-connect-windowsiotcore.md)
* [將一般 Node.js 用戶端連線至 Azure IoT Central 應用程式](howto-connect-nodejs.md)