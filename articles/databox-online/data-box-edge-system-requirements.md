---
title: Microsoft Azure Data Box Edge 系統需求 | Microsoft Docs
description: 了解 Azure Data Box Edge 的軟體和網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: d1e4af6e73c272a7ccc8996b0ccc854be64dd74b
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006354"
---
# <a name="azure-data-box-edge-system-requirements"></a>Azure 資料方塊邊緣的系統需求

本文將針對 Microsoft Azure Data Box Edge 解決方案以及連線至 Azure Data Box Edge 的用戶端，說明其各自的重要系統需求。 我們建議您先仔細檢閱這些資訊，然後再部署您的 Data Box Edge。 您可以在部署和後續作業期間，視需要回來參考此資訊。

Data Box Edge 的系統需求包括：

- **主機的軟體需求**：說明支援的平台、本機設定 UI 的瀏覽器、SMB 用戶端，以及可存取裝置的用戶端其他需求。
- **裝置的網路需求**：提供實體裝置作業的任何網路需求相關資訊。

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

## <a name="networking-port-requirements"></a>網路連接埠需求

### <a name="port-requirements-for-data-box-edge"></a>Data Box Edge 的連接埠需求

下表列出必須在您的防火牆中開啟的連接埠，以允許 SMB、雲端或管理流量。 在這個資料表中，in 或 inbound 指的是輸入用戶端要求存取裝置的方向。 *Out* 或 *outbound* 指的是 Data Box Edge 裝置在外部傳送資料到部署以外的方向 (例如，輸出到網際網路)。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge 的連接埠需求

Azure IoT Edge 允許使用支援的 IoT Hub 通訊協定，進行從內部部署 Edge 裝置到 Azure 雲端的輸出通訊。 只有針對 Azure IoT 中樞需要將訊息向下推送到 Azure IoT Edge 裝置的特定案例，才需要輸入通訊。

針對裝載 Azure IoT Edge 執行階段的伺服器，使用下表來進行連接埠設定：

| 連接埠號碼 | 內或外 | 連接埠範圍 | 必要項 | 指引 |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| 外       | WAN        | 是      | 開啟來佈建 IoT Edge 的輸出。 使用手動指令碼或 Azure IoT 裝置佈建服務 (DPS) 時，就需要此設定。|

如需完整資訊，請參閱[適用於 IoT Edge 部署的防火牆和連接埠設定規則](https://docs.microsoft.com/azure/iot-edge/troubleshoot)。

## <a name="url-patterns-for-firewall-rules"></a>防火牆規則的 URL 模式

網路系統管理員通常可以根據 URL 模式設定進階防火牆規則，來篩選輸入和輸出流量。 您的 Data Box Edge 裝置和該服務都取決於其他 Microsoft 應用程式，例如 Azure 服務匯流排、Azure Active Directory 存取控制、儲存體帳戶和 Microsoft Update 伺服器。 與這些應用程式相關聯的 URL 模式可以用來設定防火牆規則。 請務必了解與這些應用程式相關聯的 URL 模式可以變更。 這些變更需要網路系統管理員視需要來監視和更新 Data Box Edge 的防火牆規則。

我們建議您在大部分情況下，根據 Data Box Edge 固定 IP 位址來為輸出流量設定防火牆規則。 不過，您可以使用下列資訊設定建立安全環境所需的進階防火牆規則。

> [!NOTE]
> - 裝置 (來源) IP 應該一律設定為所有啟用雲端功能的網路介面。
> - 目的地 IP 應該設為 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

### <a name="url-patterns-for-gateway-feature"></a>閘道功能的 URL 模式

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>計算功能的 URL 模式

| URL 模式                      | 元件或功能                     |   
|----------------------------------|---------------------------------------------|
| https://mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 容器登錄 (必要)               |
| https://\*.azurecr.io                     | 個人和協力廠商容器登錄 (選擇性) | 
| https://\*.azure-devices.net              | IoT 中樞存取權 (必要)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Azure government 的閘道的 URL 模式

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>用於計算的 Azure Government 的 URL 模式

| URL 模式                      | 元件或功能                     |  
|----------------------------------|---------------------------------------------|
| https://mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft 容器登錄 (必要)               |
| https://\*.azure-devices.us              | IoT 中樞存取權 (必要)           |
| https://\*.azurecr.us                    | 個人和協力廠商容器登錄 (選擇性) | 

## <a name="internet-bandwidth"></a>網際網路頻寬

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>計算調整大小考量

使用您開發和測試您的解決方案時的體驗，以確保資料方塊的邊緣裝置上沒有足夠的容量，並從您的裝置獲得最佳效能。

您應該考慮的因素包括：

- **容器細節**-思考下列。

    - 您的工作負載中，有多少容器？ 您可能有幾個需要大量資源的對照的輕量級容器很多。
    - 配置給這些容器與為何他們耗用的資源的資源有哪些？
    - 您的容器共用多少層級？
    - 有未使用的容器嗎？ 已停止的容器仍會佔用磁碟空間。
    - 您的容器寫入以何種語言？
- **處理的資料大小**-資料量將您的容器處理嗎？ 將此資料會消耗磁碟空間，或將會在記憶體中處理資料？
- **預期的效能**-您的解決方案所需的效能特性為何？ 

若要了解及調整您的解決方案的效能，您可以使用：

- 在 Azure 入口網站中可用的計算度量。 請移至您的資料方塊邊緣的資源，然後移至**監視 > 計量**。 看看**Edge 計算-記憶體使用量**並**Edge 計算-CPU 百分比**若要了解可用的資源和如何所耗用的資源取得。
- 這類裝置的 PowerShell 介面透過監視命令：

    - `dkr` 若要取得即時資料流的容器資源使用量統計資料的統計資料。 此命令支援 CPU、 記憶體使用量、 記憶體限制和網路 IO 的計量。
    - `dkr system df` 若要取得之使用的磁碟空間量的相關的資訊。 
    - `dkr image [prune]` 若要清除未使用的映像，並釋出空間。
    - `dkr ps --size` 若要檢視執行中容器的近似大小。 

    如需有關可用命令的詳細資訊，請移至[監視和疑難排解計算模組](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules)。

最後，請確定您在您的資料集上驗證您的解決方案，並量化資料方塊的邊緣上的效能，在生產環境中部署之前。


## <a name="next-step"></a>後續步驟

- [部署您的 Azure 資料方塊邊緣](data-box-edge-deploy-prep.md)
