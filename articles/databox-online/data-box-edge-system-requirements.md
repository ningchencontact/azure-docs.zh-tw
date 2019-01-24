---
title: Microsoft Azure Data Box Edge 系統需求 | Microsoft Docs
description: 了解 Azure Data Box Edge 的軟體和網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/14/2019
ms.author: alkohli
ms.openlocfilehash: 60c4b22fb34a66a0ff68db26030be0e0ea3c0066
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470234"
---
# <a name="azure-data-box-edge-system-requirements-preview"></a>Azure Data Box Edge 系統需求 (預覽)

本文將針對 Microsoft Azure Data Box Edge 解決方案以及連線至 Azure Data Box Edge 的用戶端，說明其各自的重要系統需求。 我們建議您先仔細檢閱這些資訊，然後再部署您的 Data Box Edge。 您可以在部署和後續作業期間，視需要回來參考此資訊。

Data Box Edge 的系統需求包括：

- **主機的軟體需求**：說明支援的平台、本機設定 UI 的瀏覽器、SMB 用戶端，以及可存取裝置的用戶端其他需求。
- **裝置的網路需求**：提供實體裝置作業的任何網路需求相關資訊。

> [!IMPORTANT]
> Data Box Edge 目前處於預覽狀態。 部署此解決方案之前，請先檢閱[預覽版使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="supported-os-for-clients-connected-to-device"></a>支援的 OS (適用於連線至裝置的用戶端)

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>支援的通訊協定 (適用於存取裝置的用戶端)

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>支援的儲存體帳戶

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>支援的儲存體類型

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>本機 Web UI 的受支援瀏覽器

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="port-configuration-for-data-box-edge"></a>Data Box Edge 的連接埠設定

下表列出必須在您的防火牆中開啟的連接埠，以允許 SMB、雲端或管理流量。 在這個資料表中，in 或 inbound 指的是輸入用戶端要求存取裝置的方向。 *Out* 或 *outbound* 指的是 Data Box Edge 裝置在外部傳送資料到部署以外的方向 (例如，輸出到網際網路)。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="port-configuration-for-iot-edge"></a>IoT Edge 的連接埠設定

Azure IoT Edge 允許使用支援的 IoT Hub 通訊協定，進行從內部部署 Edge 裝置到 Azure 雲端的輸出通訊。 只有針對 Azure IoT 中樞需要將訊息向下推送到 Azure IoT Edge 裝置的特定案例，才需要輸入通訊。

針對裝載 Azure IoT Edge 執行階段的伺服器，使用下表來進行連接埠設定：

| 連接埠號碼 | 內或外 | 連接埠範圍 | 必要 | 指引 |
|----------|-----------|------------|----------|----------|
| TCP 5671 (AMQP)| 外       | WAN        | 是      | IoT Edge 的預設通訊協定。 如果未設定 Azure IoT Edge 使用其他支援的通訊協定，或者 AMQP 是所需的通訊協定，則必須是開啟狀態。 <br>IoT Edge 不支援適用於 AMQP 的 5672。 <br>當 Azure IoT Edge 使用不同的 IoT Hub 已支援通訊協定時，請封鎖此連接埠。 |
| TCP 443 (HTTPS)| 外       | WAN        | 是      | 開啟來佈建 IoT Edge 的輸出。 假設您具有透明的閘道，其中具有可能會傳送方法要求的分葉裝置。 在此情況下，無須對外部網路開啟連接埠 443，即可連線至 IoT 中樞，或透過 Azure IoT Edge 提供 IoT 中樞服務。 因此，可將傳入規則限制為只開啟來自內部網路的輸入。 |
| TCP 5671 (AMQP) | 在        |            | 否       | 應該封鎖輸入連線。|
| TCP 443 (HTTPS) | 在        |            | 在某些情況下，請參閱註解 | 輸入連線應該只會針對特定案例加以開啟。 如果無法設定非 HTTP 通訊協定 (例如 AMQP、MQTT)，則可使用連接埠 443，透過 WebSocket 來傳送訊息。 |

如需完整資訊，請參閱[適用於 IoT Edge 部署的防火牆和連接埠設定規則](https://docs.microsoft.com/azure/iot-edge/troubleshoot)。

## <a name="url-patterns-for-firewall-rules"></a>防火牆規則的 URL 模式

網路系統管理員通常可以根據 URL 模式設定進階防火牆規則，來篩選輸入和輸出流量。 您的 Data Box Edge 裝置和該服務都取決於其他 Microsoft 應用程式，例如 Azure 服務匯流排、Azure Active Directory 存取控制、儲存體帳戶和 Microsoft Update 伺服器。 與這些應用程式相關聯的 URL 模式可以用來設定防火牆規則。 請務必了解與這些應用程式相關聯的 URL 模式可以變更。 這些變更需要網路系統管理員視需要來監視和更新 Data Box Edge 的防火牆規則。

我們建議您在大部分情況下，根據 Data Box Edge 固定 IP 位址來為輸出流量設定防火牆規則。 不過，您可以使用下列資訊設定建立安全環境所需的進階防火牆規則。

> [!NOTE]
> - 裝置 (來源) IP 應該一律設定為所有啟用雲端功能的網路介面。
> - 目的地 IP 應該設為 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

### <a name="url-patterns-for-gateway-feature"></a>閘道功能的 URL 模式

|    URL 模式                                                                                                                                                                                                                                                                                                                                                                                                                                                      |    元件或功能                                                                           |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                                                                           |    Azure Data Box Edge 服務<br>Azure 服務匯流排<br>驗證服務                           |
|    http://\*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                                |    裝置啟用                                                                                    |
|    http://crl.microsoft.com/pki/\*<br>http://www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    憑證撤銷                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                            |    Azure 儲存體帳戶和監視                                                                |
|    http://windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Microsoft Update 伺服器                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    Akamai CDN                                                                                           |
|    https://\*.partners.extranet.microsoft.com/\*                                                                                                                                                                                                                                                                                                                                                                                                                    |    支援封裝                                                                                      |
|    http://\*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                                     |    Windows 中的遙測服務，請參閱客戶經驗和診斷遙測的更新      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                         |


### <a name="url-patterns-for-compute-feature"></a>計算功能的 URL 模式

| URL 模式                      | 元件或功能                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | Microsoft 容器登錄 (必要)               |   |
| https://\*.azurecr.io                     | 個人和協力廠商容器登錄 (選擇性) |   |
| https://\*.azure-devices.net              | IoT 中樞存取權 (必要)                             |   |

## <a name="internet-bandwidth"></a>網際網路頻寬

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>後續步驟

* [部署 Azure Data Box Edge](data-box-Edge-deploy-prep.md)
