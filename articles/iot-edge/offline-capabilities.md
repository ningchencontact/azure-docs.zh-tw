---
title: 離線操作裝置 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 裝置及模組針對延長時間期間無網際網路連線執行的方式，以及 IoT Edge 讓一般 IoT 裝置也能離線執行的方式。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051963"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>了解 IoT Edge 裝置、 模組和子裝置的擴充離線功能

Azure IoT Edge 支援 IoT Edge 裝置上的擴充離線作業，而且也可讓子系的非 IoT Edge 裝置上的離線作業。 只要 IoT Edge 裝置已有機會連線到 IoT 中樞，該裝置及任何子裝置便能在間歇性或無網際網路連線的情況下繼續運作。 


## <a name="how-it-works"></a>運作方式

當 IoT Edge 裝置進入離線模式時，IoT Edge 中樞便會擔任三個角色。 首先，它會儲存任何可能前往上游的訊息，並會持續儲存到裝置重新連線為止。 其次，它會代替 IoT 中樞驗證模組及子裝置，使其能繼續執行。 第三，它可在子裝置之間進行平常需要透過 IoT 中樞進行的通訊。 

下列範例示範 IoT Edge 案例於離線模式執行的方式：

1. **設定裝置**

   IoT Edge 裝置會自動啟用離線功能。 若要將該功能延伸至其他 IoT 裝置，您需要宣告 IoT 中樞內裝置間的父子關聯。 然後，您可以設定信任其指派的父代裝置，並路由傳送裝置到雲端通訊透過父代為閘道的下層裝置。 

2. **使用 IoT 中樞的同步處理**

   在安裝完 IoT Edge 執行階段之後，IoT Edge 裝置至少需要處於線上狀態一次，以和 IoT 中樞進行同步處理。 在此同步中，IoT Edge 裝置會取得指派給它的任何子裝置詳細資料。 IoT Edge 裝置也會安全地更新其本機快取，允取離線作業，並會擷取遙測訊息本機存放區的設定。 

3. **離線**

   從 IoT 中樞中斷連線時，IoT Edge 裝置、其部署的模組，以及任何子 IoT 裝置都能無限期的繼續執行。 模組及子裝置可在離線狀態時，透過向 IoT Edge 中樞進行驗證來啟動或重新啟動。 至 IoT 中樞的遙測繫結上游會儲存在本機。 模組或子 IoT 裝置之間的通訊則會透過直接方法或訊息維持。 

4. **重新連線，然後重新同步處理與 IoT 中樞**

   一旦與 IoT 中樞的連線還原，IoT Edge 裝置便會再次進行同步處理。 本機儲存訊息會依照儲存時的相同順序進行傳遞。 模組和裝置所需屬性及回報屬性間的任何差異都會進行協調。 IoT Edge 裝置會更新任何對其受指派子 IoT 裝置集合進行的變更。

## <a name="restrictions-and-limits"></a>限制

本文描述的延伸離線功能適用於 [IoT Edge 1.0.4 版或更高版本](https://github.com/Azure/azure-iotedge/releases)。 先前版本具備一部分的離線功能。 不具備延伸離線功能的現有 IoT Edge 裝置無法透過變更執行階段版本來進行升級，而必須使用新的 IoT Edge 裝置識別重新設定，才能獲得這些功能。 

延伸離線支援適用於所有可使用 IoT 中樞的區域 (美國東部**除外**)。

只有非 IoT Edge 裝置可以新增為子系的裝置。 

IoT Edge 裝置及其受指派的子裝置可在一開始的首次同步處理之後，於離線狀態下無限期運作。但是，儲存訊息則取決於存留時間 (TTL) 設定及可用於儲存訊息的磁碟空間。 

## <a name="set-up-parent-and-child-devices"></a>設定父系和子系的裝置

針對 IoT Edge 裝置來擴充其離線的擴充的功能，可子 IoT 裝置，您需要完成兩個步驟。 首先，宣告父子式關聯性，在 Azure 入口網站中。 第二，建立父裝置和任何的下層裝置之間的信任關係，然後設定與瀏覽父代為閘道的裝置到雲端通訊。 

### <a name="assign-child-devices"></a>指派子裝置

子裝置可以是任何非 IoT Edge 裝置，註冊到相同 IoT 中樞。 父裝置可以有多個下層裝置，但子裝置只有一個父代。 有三個選項來設定子裝置到 edge 裝置： 透過 Azure 入口網站中，使用 Azure CLI，或使用 IoT 中樞服務 SDK。 

下列各節提供如何宣告適用於現有的 IoT 裝置在 IoT 中樞的父子式關聯性的範例。 如果您要為您的子女建立新的裝置身分識別，裝置，請參閱[驗證至 Azure IoT 中樞的下游裝置](how-to-authenticate-downstream-device.md)如需詳細資訊。

#### <a name="option-1-iot-hub-portal"></a>選項 1：IoT 中樞入口網站

建立新的裝置時，您可以宣告父子式關聯性。 或針對現有的裝置，您可以宣告的父 IoT Edge 裝置的裝置詳細資料頁面的關聯性或子 IoT 裝置。 

   ![從 IoT Edge 裝置詳細資料頁面管理子裝置](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>選項 2：使用`az`命令列工具

使用[Azure 命令列介面](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)具有[IoT 擴充功能](https://github.com/azure/azure-iot-cli-extension)(v0.7.0 或更新版本)，您可以管理與父子式關聯性[裝置身分識別](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)子命令。 下列範例會使用查詢來指派所有的非 IoT Edge 裝置，在中樞的 IoT Edge 裝置的子系裝置。 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

您可以修改[查詢](../iot-hub/iot-hub-devguide-query-language.md)選取裝置的不同子集。 此命令可能需要幾秒鐘的時間，如果您指定大量的裝置。

#### <a name="option-3-use-iot-hub-service-sdk"></a>選項 3：使用 IoT 中樞服務 SDK 

最後，您可以在其中管理父子式關聯性，以程式設計方式使用C#、 Java 或 Node.js IoT 中樞服務 SDK。 以下是[將子裝置指派的範例](https://aka.ms/set-child-iot-device-c-sharp)使用C#SDK。

### <a name="set-up-the-parent-device-as-a-gateway"></a>設定父裝置作為閘道

您可以將做為透明的閘道，其中子裝置在 IoT 中樞有自己的身分識別，但透過其父代透過雲端進行通訊的父子式關聯性。 安全通訊，讓子裝置必須能夠驗證父裝置來自受信任的來源。 否則，第三方可以設定惡意裝置，以模擬父代，並攔截通訊。 

下列文章中詳細說明如何建立此信任關係： 
* [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
* [將下游 （子系） 裝置連接至 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>指定 DNS 伺服器 

若要改善穩定性，強烈建議您指定在您的環境中使用的 DNS 伺服器位址。 請參閱兩個選項可[疑難排解文章中設定 DNS 伺服器](troubleshoot.md#resolution-7)。

## <a name="optional-offline-settings"></a>選擇性離線設定

如果您的裝置離線，IoT Edge 父裝置會儲存所有的裝置到雲端訊息，直到重新建立連接。 IoT Edge 中樞模組會管理儲存體和轉送離線的訊息。 對於可能會離線很長的時間的裝置，請藉由設定兩個 IoT Edge 中樞最佳化效能。 

首先，增加的即時設定，好讓 IoT Edge 中樞會保留為您的裝置重新連線時間夠長的訊息的時間。 然後，增加額外的磁碟空間來儲存訊息。 

### <a name="time-to-live"></a>存留時間

存留時間設定是在過期前，訊息所能等待傳遞的時間長度。 預設為 7200 秒 (兩小時)。 最大值只受限於整數變數，也就是大約 2 億的最大值。 

此設定是 IoT Edge 中樞的所需設定，會儲存在模組對應項中。 在 Azure 入口網站中或直接在部署資訊清單中，您可以設定它。 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>其他離線儲存體

根據預設，訊息會儲存在 IoT Edge 中樞的容器檔案系統。 若該儲存體數量不足以應付您的離線需求，您可以在 IoT Edge 裝置上專用本機存放區。 為 IoT Edge 中樞建立環境變數，指向容器中的儲存體資料夾。 然後，使用建立選項，將該儲存體資料夾與主機電腦上的資料夾繫結。 

您可以在 Azure 入口網站內的 [設定進階 Edge 執行階段設定]  區段設定環境變數及建立 IoT Edge 中樞模組的選項。 或者，您也可以在部署資訊清單中直接設定。 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

使用您的主機和模組儲存體路徑取代 `<HostStoragePath>` 和 `<ModuleStoragePath>`；主機和模組儲存體路徑都必須是絕對路徑。 在建立選項中，將主機和模組儲存體路徑繫結在一起。 接著，建立指向模組儲存體路徑的環境變數。  

例如，`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示您主機系統上的 **/etc/iotedge/storage** 目錄會對應至容器上的 **/iotedge/storage/** 目錄。 或是再以 Windows 系統舉一個例子，`"Binds":["C:\\temp:C:\\contemp"]` 表示您主機系統上的 **C:\\temp** 目錄會對應至容器上的 **C:\\contemp** 目錄。 

您也可以從 [Docker 文件](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)中找到更多有關建立選項的詳細資料。

## <a name="next-steps"></a>後續步驟

深入了解如何設定透明父子式裝置連線的閘道： 

* [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)
* [驗證至 Azure IoT 中樞的下游裝置](how-to-authenticate-downstream-device.md)
* [將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)
