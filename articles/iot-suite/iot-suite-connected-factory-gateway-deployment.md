---
title: "部署您已連線的處理站的閘道-Azure |Microsoft 文件"
description: "如何部署閘道，以在 Windows 或 Linux 來啟用連線至已連線的 factory 上預先設定的解決方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: dobett
ms.openlocfilehash: 1506488193638400af7c71b3ecd00e99512daa62
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-preconfigured-solution-on-windows-or-linux"></a>部署預先設定連接的工廠方案在 Windows 或 Linux 上邊緣閘道

您需要兩個的軟體元件，才能部署的邊緣閘道*連接的工廠*預先設定的方案：

- *OPC Proxy*建立連線的處理站的連線。 OPC Proxy 然後等候來自整合的 OPC 瀏覽器連接的工廠方案入口網站中執行的命令和控制訊息。

- *OPC 發行者*會連接到現有的內部部署 OPC UA 伺服器並將它們從遙測訊息轉送連線 factory。 您可以連接 OPC 傳統裝置使用[OPC OPC UA 的傳統介面卡](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md)。

這兩個元件的開放原始碼，而且會在 GitHub 上的來源及 DockerHub 上的 Docker 容器：

| GitHub | DockerHub |
| ------ | --------- |
| [OPC 發行者](https://github.com/Azure/iot-edge-opc-publisher) | [OPC 發行者](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC Proxy](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

不是元件需要有公開 IP 位址或閘道防火牆中的開啟輸入連接埠。 OPC Proxy 和 OPC 發行者元件只會使用輸出連接埠 443。

這篇文章中的步驟會示範如何部署使用 Windows 或 Linux 上的 Docker 邊緣閘道。 閘道器可讓連線到預先設定連接的工廠方案。 您也可以使用元件未連接的 factory。

> [!NOTE]
> 這兩個元件可作為單元[Azure IoT 邊緣](https://github.com/Azure/iot-edge)。

## <a name="choose-a-gateway-device"></a>選擇 閘道裝置

如果您還沒有閘道裝置，Microsoft 建議您向合作夥伴購買商業閘道。 如需連接的工廠方案與相容的閘道裝置的清單，請瀏覽[Azure IoT 裝置類別目錄](https://catalog.azureiotsuite.com/?q=opc)。 依照裝置隨附的指示來設定閘道。

或者，使用下列指示手動設定現有的閘道裝置。

## <a name="install-and-configure-docker"></a>安裝及設定 Docker

安裝[Docker for Windows](https://www.docker.com/docker-windows) Windows 型閘道裝置上使用封裝管理員，將安裝在您以 Linux 為基礎的閘道裝置上的 docker。

Docker for Windows 安裝期間選取共用使用 Docker 主機電腦上的磁碟機。 下列螢幕擷取畫面顯示共用**D** Windows 系統以允許存取從 docker 容器內的主機磁碟機上的磁碟機：

![安裝 Docker for Windows](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> 您也可以執行此步驟安裝 docker 從之後**設定**對話方塊。 以滑鼠右鍵按一下**Docker** Windows 系統匣圖示，然後選擇 **設定**。

如果您使用 Linux，不需要額外組態才能啟用檔案系統的存取權。

在 Windows 中，使用 Docker 您共用的磁碟機上建立資料夾，在 Linux 上建立資料夾之下的根檔案系統。 本逐步解說是指與此資料夾`<SharedFolder>`。

當您參考`<SharedFolder>`在 Docker 命令中，務必使用正確的語法適用於您作業系統。 以下是兩個範例，一個用於 Windows，一個適用於 Linux:

- 如果您正在使用資料夾`D:\shared`上 Windows，做為您`<SharedFolder>`，Docker 命令語法是`//d/shared`。

- 如果您正在使用資料夾`/shared`on Linux 做為您`<SharedFolder>`，Docker 命令語法是`/shared`。

如需詳細資訊，請參閱[使用磁碟區](https://docs.docker.com/engine/admin/volumes/volumes/)docker 引擎的參考。

## <a name="configure-the-opc-components"></a>設定 OPC 元件

安裝 OPC 元件之前，請完成下列步驟來準備您的環境：

1. 若要完成閘道部署，您需要**iothubowner** IoT 中樞，您已連線的處理站的部署中的連接字串。 在[Azure 入口網站](http://portal.azure.com/)，瀏覽至您的 IoT 中樞部署連線的 factory 方案時建立的資源群組中。 按一下 [共用存取原則] 以存取 **iothubowner** 連接字串︰

    ![找出 IoT 中樞連接字串](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    複製**連接字串-主索引鍵**值。

1. 若要讓 docker 容器之間的通訊，您需要使用者定義的橋接網路。 若要建立您的容器的橋接網路，請在命令提示字元執行下列命令：

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    若要確認**iot_edge**建立橋接網路時，執行下列命令：

    ```cmd/sh
    docker network ls
    ```

    您**iot_edge**橋接網路隨附於網路清單。

若要執行 OPC 發行者，請在命令提示字元執行下列命令：

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [OPC 發行者 GitHub](https://github.com/Azure/iot-edge-opc-publisher)和[docker 執行參考](https://docs.docker.com/engine/reference/run/)提供有關的詳細資訊：

  - 指定的容器名稱前面的 docker 命令列選項 (`microsoft/iot-edge-opc-publisher:2.1.3`)。
  - 容器名稱後指定 OPC 發行者命令列參數的意義 (`microsoft/iot-edge-opc-publisher:2.1.3`)。

- `<IoTHubOwnerConnectionString>`是**iothubowner**共用存取原則的連接字串從 Azure 入口網站。 您在上一個步驟中複製這個連接字串。 您只需要此連接字串的 OPC 「 發行者 」 的第一次的執行。 在後續的執行，應該省略它因為它會有安全性風險。

- `<SharedFolder>`您使用，而且它的語法節將說明[安裝及設定 Docker](#install-and-configure-docker)。 OPC 發行者使用`<SharedFolder>`讀取 OPC 發行者組態檔、 寫入記錄檔，並使兩者這些檔案可用容器之外。

- OPC 發行者讀取其組態從**publishednodes.json**檔案，您應該將它放在`<SharedFolder>/docker`資料夾。 此組態檔會定義哪些 OPC UA 節點資料 OPC 發行者必須訂閱特定 OPC UA 伺服器上。

- 每當 OPC UA 伺服器通知 OPC 發行者的資料變更時，新值會傳送到 IoT 中樞。 根據批次處理的設定，可能會先 OPC 發行者累積資料之前傳送資料到 IoT 中樞批次中。

- 完整語法**publishednodes.json**檔案會說明[OPC 發行者](https://github.com/Azure/iot-edge-opc-publisher)GitHub 上的頁面。

    下列程式碼片段顯示的簡單範例**publishednodes.json**檔案。 這個範例示範如何發行**CurrentTime**從 OPC UA 伺服器主機名稱與值**win10pc**:

    ```json
    [
      {
        "EndpointUrl": "opc.tcp://win10pc:48010",
        "OpcNodes": [
          {
            "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
          }
        ]
      }
    ]
    ```

    在**publishednodes.json**檔案，OPC UA 伺服器指定的端點 URL。 如果您指定主機名稱使用的主機名稱的標籤 (例如**win10pc**) 與前面的範例，而不是 IP 位址，在容器中的網路位址解析必須能夠解析為 IP 位址的此主機名稱標籤。

- Docker 不支援 NetBIOS 名稱解析，唯一的 DNS 名稱解析。 如果您在網路上沒有 DNS 伺服器，您可以使用先前的命令列範例中顯示的因應措施。 先前的命令列範例使用`--add-host`參數新增至容器主機檔案項目。 此項目可讓主機名稱查閱給定`<OpcServerHostname>`、 指定的 IP 位址來解決`<IpAddressOfOpcServerHostname>`。

- OPC UA 會使用 X.509 憑證進行驗證和加密。 您需要將 OPC 發行者憑證放置在您所連接，以確保其信任發行者 OPC OPC UA 伺服器。 OPC 「 發行者 」 憑證存放區位於`<SharedFolder>/CertificateStores`資料夾。 您可以找到的 OPC 發行者憑證中`trusted/certs`資料夾中的`CertificateStores`資料夾。

  若要設定 OPC UA 伺服器的步驟取決於您使用的裝置。 OPC UA 伺服器的使用者手冊 》，通常會記錄這些步驟。

若要安裝 OPC Proxy，請在命令提示字元執行下列命令：

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

您只需要執行一次在系統上安裝。

您可以使用下列命令執行 OPC Proxy:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

OPC Proxy 會將連接字串儲存在安裝期間。 在後續的執行，因為它會有安全性風險，應該省略連接字串。

## <a name="enable-your-gateway"></a>啟用您的閘道

完成下列步驟，以讓您預先設定連接的工廠方案中的閘道：

1. 這兩個元件執行時，瀏覽至**自己 OPC UA 伺服器連接**連接的工廠方案入口網站頁面中。 只有系統管理員在方案中使用此頁面。 輸入發行者的端點 URL (opc.tcp://publisher: 62222) 按一下**連接**。

1. 建立連接的 factory 入口網站和 OPC 發行者之間的信任關係。 當您看到憑證警告時，按一下**繼續**。 接下來，您會看到錯誤 OPC 發行者不信任 UA Web 用戶端。 若要解決這個錯誤，將複製**UA Web 用戶端**憑證從`<SharedFolder>/CertificateStores/rejected/certs`資料夾`<SharedFolder>/CertificateStores/trusted/certs`閘道上的資料夾。 您不需要重新啟動該閘道。

您現在可以從雲端連線到閘道，而且您也準備好在解決方案中新增 OPC UA 伺服器。

## <a name="add-your-own-opc-ua-servers"></a>新增您自己的 OPC UA 伺服器

若要加入您自己的 OPC 美國連接工廠的伺服器預先設定的方案：

1. 瀏覽至**連接 OPC UA 伺服器**連接的工廠方案入口網站頁面中。 請遵循相同的步驟上一節，以建立連接的 factory 入口網站與 OPC UA 伺服器之間的信任關係。

    ![解決方案入口網站](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. 瀏覽 OPC UA 伺服器的 OPC UA 節點樹狀目錄，以滑鼠右鍵按一下您想要將傳送至已連線的處理站，並選取 OPC 節點**發行**。

1. 現在會從閘道裝置傳送遙測。 您可以檢視中的遙測**Factory 位置**之下的已連線的 factory 入口網站畫面**新的處理站**。

## <a name="next-steps"></a>後續步驟

若要深入了解預先設定連接的工廠方案的架構，請參閱[連接的工廠預先設定方案的逐步解說](https://docs.microsoft.com/en-us/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough)。

了解 [OPC 發行者參考實作](https://docs.microsoft.com/en-us/azure/iot-suite/iot-suite-connected-factory-publisher)。