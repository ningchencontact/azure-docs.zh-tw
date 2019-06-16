---
title: 建立透明閘道裝置 - 使用 Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 裝置作為可處理來自下游裝置之資訊的透明閘道
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058371"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>設定 IoT Edge 裝置作為透明閘道

本文章提供詳細的指示來設定 IoT Edge 裝置作為透明閘道的其他裝置與 IoT 中樞進行通訊。 在本文中，*IoT Edge 閘道*一詞是指當作透明閘道使用的 IoT Edge 裝置。 如需詳細資訊，請參閱 <<c0> [ 如何使用 IoT Edge 裝置可以作為閘道](./iot-edge-as-gateway.md)。

>[!NOTE]
>目前狀況：
> * 已啟用 Edge 的裝置無法連線到 IoT Edge 閘道。 
> * 下游裝置無法使用檔案上傳。

有三個一般步驟來設定成功的透明閘道連線。 本文章涵蓋的第一個步驟：

1. **閘道裝置必須能夠安全地連接到下游裝置、 收到來自下游的裝置，並將訊息路由至適當的目的地。**
2. 下游裝置必須具有能夠向 IoT 中樞，而且知道要透過其閘道裝置進行通訊的裝置身分識別。 如需詳細資訊，請參閱 <<c0> [ 驗證至 Azure IoT 中樞的下游裝置](how-to-authenticate-downstream-device.md)。
3. 下游裝置必須能夠安全地連線到其閘道裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。


要當做閘道運作的裝置，讓它必須能夠安全地連線到其下游裝置。 Azure IoT Edge 可讓您使用公開金鑰基礎結構 (PKI)，設定這些裝置之間的安全連線。 在此案例中，我們將允許下游裝置連線至作為透明閘道的 IoT Edge 裝置。 若要維持合理的安全性，下游裝置應該確認閘道裝置的身分識別。 此身分識別檢查可防止您的裝置連線到可能是惡意的閘道。

下游裝置可以是使用 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)雲端服務建立身分識別的任何應用程式或平台。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 就所有實際用途而言，下游裝置甚至可以是在 IoT Edge 閘道裝置本身執行的應用程式。 

您可以建立任何憑證基礎結構，來啟用裝置閘道拓撲所需的信任。 在本文中，我們假設的是相同的憑證安裝程式，您會使用它來啟用[X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)在 IoT 中樞，這牽涉到的特定 IoT 中樞 （IoT 中樞根 CA），一系列的簽署憑證相關聯的 X.509 CA 憑證使用此 CA 和 CA IoT Edge 裝置。

![閘道憑證設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>「 根 CA 」 這整篇文章所使用的詞彙是指最上層的授權單位的公開憑證的 PKI 憑證鏈結，並不一定是同步發行的憑證授權單位根憑證。 在許多情況下，它可以是實際中繼 CA 公開憑證。 

閘道提供其 IoT Edge 裝置 CA 憑證，以在下游裝置期間初始化的連線。 下游裝置會檢查以確定 IoT Edge 裝置 CA 憑證之憑證所簽署的根 CA。 此程序可讓下游的裝置，以確認閘道來自受信任的來源。

下列步驟會引導您建立的憑證並將它們安裝在正確的位置，在閘道上的程序。 您可以使用任何電腦產生憑證，然後再將其複製到您的 IoT Edge 裝置。 

## <a name="prerequisites"></a>必要條件

設定為閘道的 Azure IoT Edge 裝置。 使用 IoT Edge 的安裝步驟，針對下列作業系統的其中一個：
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

這篇文章是指*閘道主機名稱*的數個點。 閘道主機名稱已在中宣告**hostname** config.yaml 檔案在 IoT Edge 閘道裝置上的參數。 它用來建立憑證，在本文中，指下游裝置的連接字串中。 閘道主機名稱必須解析成 IP 位址，使用 DNS 或 host 檔案項目。

## <a name="generate-certificates-with-windows"></a>使用 Windows 產生憑證

使用這一節的步驟，以產生在 Windows 上的測試憑證。 您可以使用 Windows 電腦來產生憑證，並再透過將它們複製到任何支援的作業系統上執行任何 IoT Edge 裝置。 

本節中所產生的憑證是僅供測試用途。 

### <a name="install-openssl"></a>安裝 OpenSSL

在您用來產生憑證的電腦上，安裝 Windows 適用的 OpenSSL。 如果您已經在 Windows 裝置上安裝 OpenSSL，您可能略過此步驟中，但請確定該 openssl.exe 位於您的 PATH 環境變數。 

您可以透過數種方式來安裝 OpenSSL：

* **較簡便：** 下載並安裝任何[協力廠商 OpenSSL 二進位檔](https://wiki.openssl.org/index.php/Binaries)，例如，從[SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/)。 將 openssl.exe 的完整路徑加入至您的 PATH 環境變數。 
   
* **建議配備：** 自行下載 OpenSSL 原始程式碼並在您的電腦上組建二進位檔，或是透過 [vcpkg](https://github.com/Microsoft/vcpkg) 來執行。 下列指示會使用 vcpkg 下載原始程式碼，並在 Windows 電腦上編譯並安裝 OpenSSL，且各項作業都能以輕鬆的步驟完成。

   1. 瀏覽至要安裝 vcpkg 的目錄。 我們將此目錄稱之為 *\<VCPKGDIR>* 。 依照指示下載並安裝 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. Vcpkg 被安裝之後，執行下列命令，從 powershell 提示中的 Windows x64 安裝 OpenSSL 套件。 安裝通常需要約 5 分鐘的時間。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. 將 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 新增至您的 PATH 環境變數，讓 openssl.exe 檔案可供叫用。

### <a name="prepare-creation-scripts"></a>準備建立指令碼

Azure IoT Edge 的 git 存放庫包含的指令碼，您可以使用來產生測試憑證。 在本節中，您要複製的 IoT Edge 存放庫，並執行指令碼。 

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 

2. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。 這些指令碼可協助您建立用來設定透明閘道的所需憑證。 使用 `git clone` 命令或[下載 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 瀏覽至您要使用的目錄。 在本文中，我們稱此目錄 *\<WRKDIR >* 。 在這個工作目錄中，將會建立所有憑證和金鑰。

4. 複製的存放庫的組態和指令碼檔案複製到您的工作目錄。 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果您已下載為 ZIP，存放庫，則資料夾名稱就是`iotedge-master`和路徑的其餘部分會相同。 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. 啟用 PowerShell 執行指令碼。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. 將指令碼用到 PowerShell 的全域命名空間的函式。
   
   ```powershell
   . .\ca-certs.ps1
   ```

   [PowerShell] 視窗會顯示警告，此指令碼所產生的憑證僅供測試之用，，和不應用於實際執行案例。

8. 確認已正確安裝 OpenSSL，並確定無法使用現有憑證的名稱衝突。 如果有問題，指令碼應會說明如何在系統上加以修正。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>建立憑證

在本節中，您要建立三個憑證，然後將它們以鏈結方式連接。 將憑證放入鏈結檔案可讓您輕鬆地在您的 IoT Edge 閘道裝置和任何下游裝置上安裝它們。  

1. 建立根 CA 憑證並將它簽署一個中繼憑證。 憑證所有會放在您的工作目錄。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此指令碼命令會建立數個憑證和金鑰檔案，但我們將特別是在本文稍後的其中一個，請參閱：
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 使用下列命令建立 IoT Edge 裝置 CA 憑證和私密金鑰。 提供閘道的主機名稱，可以在閘道裝置上的 iotedge\config.yaml 檔案中找到。 閘道主機名稱來命名檔案和憑證產生期間。 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   此指令碼命令會建立數個憑證和金鑰的檔案，其中包括我們要請參閱本文稍後的兩個：
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

既然您已將憑證，請直接跳到[在閘道上安裝憑證](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>使用 Linux 產生憑證

使用這一節的步驟，以產生在 Linux 上的測試憑證。 您可以使用 Linux 電腦產生的憑證，並再透過將它們複製到任何支援的作業系統上執行任何 IoT Edge 裝置。 

本節中所產生的憑證是僅供測試用途。 

### <a name="prepare-creation-scripts"></a>準備建立指令碼

Azure IoT Edge 的 git 存放庫包含的指令碼，您可以使用來產生測試憑證。 在本節中，您要複製的 IoT Edge 存放庫，並執行指令碼。 

1. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。 這些指令碼可協助您建立用來設定透明閘道的所需憑證。 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 瀏覽至您要使用的目錄。 我們會以整篇文章，為此目錄 *\<WRKDIR >* 。 所有的憑證和金鑰檔將會建立此目錄中。
  
3. 複製的 IoT Edge 存放庫的組態和指令碼檔案複製到您的工作目錄。

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>建立憑證

在本節中，您要建立三個憑證，然後將它們以鏈結方式連接。 將憑證放入鏈結檔案可讓您輕鬆地在您的 IoT Edge 閘道裝置和任何下游裝置上安裝它們。  

1. 建立根 CA 憑證和一個中繼憑證。 這些憑證放置在 *\<WRKDIR>* 中。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   指令碼會建立數個憑證和金鑰。 請記下其中一個，我們將在下一節中參照：
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. 使用下列命令建立 IoT Edge 裝置 CA 憑證和私密金鑰。 提供閘道的主機名稱，可以在閘道裝置上的 iotedge/config.yaml 檔案中找到。 閘道主機名稱來命名檔案和憑證產生期間。 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   指令碼會建立數個憑證和金鑰。 請記下兩個，也會討論到下一節： 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>在閘道上安裝憑證

既然您已經建立憑證鏈結，您需要將它安裝在 IoT Edge 閘道裝置上，並將 IoT Edge 執行階段設定為參考新的憑證。 

1. 從 *\<WRKDIR>* 複製下列檔案。 將其儲存在您 IoT Edge 裝置上的任何位置。 我們將您 IoT Edge 裝置上的目的地目錄稱之為 *\<CERTDIR>* 。 

   * 裝置 CA 憑證 -  `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * 裝置 CA 私密金鑰 - `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Root CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   您可以使用這類服務[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)或是想要的函式[安全複製通訊協定](https://www.ssh.com/ssh/scp/)移動憑證檔案。  如果您產生本身的 IoT Edge 裝置上的憑證，您可以略過此步驟，並使用工作目錄的路徑。

2. 開啟 IoT Edge 安全性精靈組態檔。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 設定**憑證**config.yaml 檔案至 IoT Edge 裝置上的憑證和金鑰檔的完整路徑中的屬性。 移除`#`字元之前的四行取消註解的憑證屬性。 請記住在 yaml 中的縮排是兩個空格。

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux： 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. 在 Linux 裝置，請確定使用者**iotedge**具有讀取權限持有憑證的目錄。 

## <a name="deploy-edgehub-to-the-gateway"></a>將 Edge 中樞部署至閘道

當您第一次會安裝在裝置上的 IoT Edge 時，只能有一個系統模組會自動啟動： IoT Edge 代理程式。 若要讓您的裝置當作閘道運作，您需要兩個系統模組。 如果您尚未部署任何模組至您的閘道裝置之前，建立初始的部署，以您開始第二個系統模組，IoT Edge 中樞的裝置。 因為您未新增任何模組在精靈中，但它可確保這兩個系統模組正在執行，則部署會尋找空白。 

您可以使用 `iotedge list` 命令，檢查哪些模組是在裝置上執行。 如果清單只會傳回模組**edgeAgent**不含**edgeHub**，請使用下列步驟：

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。

2. 移至 [IoT Edge]  ，並選取要作為閘道的 IoT Edge 裝置。

3. 選取 [設定模組]  。

4. 選取 [下一步]  。

5. 在 [指定路由]  頁面中，您應該會有可將所有模組中的所有訊息傳送至 IoT 中樞的預設路由。 如果沒有，請新增下列程式碼，然後選取 [下一步]  。

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. 在 [檢閱範本]  頁面中，選取 [提交]  。

## <a name="open-ports-on-gateway-device"></a>在 閘道裝置上開啟連接埠

標準的 IoT Edge 裝置不需要任何輸入的連線能力函式，因為與 IoT 中樞的所有通訊都會都透過輸出連線。 閘道裝置會不同，因為它們必須從其下游裝置接收訊息。 如果防火牆是下游的裝置之間的閘道裝置，然後通訊必須通過防火牆也可能。

閘道案例中運作，其中至少一個 IoT Edge 中樞支援的通訊協定必須開放給來自下游裝置的輸入流量。 支援的通訊協定是 MQTT、 AMQP 及 HTTPS。 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>從下游裝置路由傳送訊息
IoT Edge 執行階段可以路由傳送從下游裝置送來的訊息，就像路由傳送從模組送來的訊息一樣。 這項功能可讓您在閘道上的任何資料傳送至雲端之前，先執行模組中執行分析。 

目前，針對下游裝置所傳送的訊息，您進行路由傳送的方式是將它們與模組所傳送的訊息做區分。 模組所傳送的訊息全都包含名為 **connectionModuleId** 系統屬性，但下游裝置所傳送的訊息則無此屬性。 您可以使用路由的 WHERE 子句來排除任何包含該系統屬性的訊息。 

路由以下是範例會從任何下游裝置傳送訊息至名為模組`ai_insights`，然後從`ai_insights`到 IoT 中樞。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

如需有關訊息路由的詳細資訊，請參閱[部署模組及建立路由](./module-composition.md#declare-routes)。


## <a name="enable-extended-offline-operation"></a>啟用擴充離線的作業

開頭[v1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)IoT Edge 執行階段中，閘道裝置並連線到它的下游裝置可以設定擴充項離線作業。 

使用此功能，本機模組或下游裝置可以視需要重新驗證 IoT Edge 裝置，並使用訊息與方法與 IoT 中樞中斷連線時，即使互相通訊。 如需詳細資訊，請參閱[了解適用於 IoT Edge 裝置、模組及子裝置的擴充離線功能](offline-capabilities.md)。

若要啟用擴充的離線功能，您可以建立 IoT Edge 閘道裝置與連接的下游裝置之間的父子式關聯性。 更詳細地說明這些步驟[驗證至 Azure IoT 中樞的下游裝置](how-to-authenticate-downstream-device.md)。

## <a name="next-steps"></a>後續步驟

既然您讓 IoT Edge 裝置當作透明閘道運作，您需要將下游裝置設定為信任閘道，並將訊息傳送到該裝置。 如需詳細資訊，請參閱 <<c0> [ 下游裝置連線至 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)並[驗證至 Azure IoT 中樞的下游裝置](how-to-authenticate-downstream-device.md)。
