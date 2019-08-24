---
title: 建立透明閘道裝置 - 使用 Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 裝置作為可處理來自下游裝置之資訊的透明閘道
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e61ddd6cb51795fad564b6246fb24ea4ce48f028
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982936"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>設定 IoT Edge 裝置作為透明閘道

本文提供詳細的指示, 說明如何設定 IoT Edge 裝置做為透明閘道, 以供其他裝置與 IoT 中樞通訊。 在本文中，*IoT Edge 閘道*一詞是指當作透明閘道使用的 IoT Edge 裝置。 如需詳細資訊, 請參閱[如何使用 IoT Edge 裝置作為閘道](./iot-edge-as-gateway.md)。

>[!NOTE]
>目前狀況：
> * 已啟用 Edge 的裝置無法連線到 IoT Edge 閘道。 
> * 下游裝置無法使用檔案上傳。

設定成功的透明閘道連線有三個一般步驟。 本文涵蓋第一個步驟:

1. **閘道裝置必須能夠安全地連線到下游裝置、接收來自下游裝置的通訊, 以及將訊息路由傳送至適當的目的地。**
2. 下游裝置必須具有裝置身分識別, 才能使用 IoT 中樞進行驗證, 並知道要透過其閘道裝置進行通訊。 如需詳細資訊, 請參閱[驗證下游裝置以 Azure IoT 中樞](how-to-authenticate-downstream-device.md)。
3. 下游裝置必須能夠安全地連接到其閘道裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。


若要讓裝置做為閘道運作, 它必須能夠安全地連線到其下游裝置。 Azure IoT Edge 可讓您使用公開金鑰基礎結構 (PKI)，設定這些裝置之間的安全連線。 在此案例中，我們將允許下游裝置連線至作為透明閘道的 IoT Edge 裝置。 為了維持合理的安全性, 下游裝置應該確認閘道裝置的身分識別。 此身分識別檢查會防止您的裝置連線到潛在的惡意閘道。

下游裝置可以是使用 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)雲端服務建立身分識別的任何應用程式或平台。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 就所有實際用途而言，下游裝置甚至可以是在 IoT Edge 閘道裝置本身執行的應用程式。 

您可以建立任何憑證基礎結構，來啟用裝置閘道拓撲所需的信任。 在本文中, 我們假設您要用來在 IoT 中樞中啟用[X.509 ca 安全性](../iot-hub/iot-hub-x509ca-overview.md)的相同憑證設定, 這牽涉到與特定 IoT 中樞 (iot 中樞根 CA) 相關聯的 x.509 CA 憑證, 這是一系列使用此 CA 簽署的憑證和 IoT Edge 裝置的 CA。

![閘道憑證設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>本文中使用的「根 CA」一詞指的是 PKI 憑證鏈的最高授權公開憑證, 而不一定是聯合憑證授權單位的憑證根。 在許多情況下, 它實際上是中繼 CA 公開憑證。 

閘道會在連線起始期間, 將其 IoT Edge 裝置 CA 憑證提供給下游裝置。 下游裝置會進行檢查, 以確定 IoT Edge 的裝置 CA 憑證是由根 CA 憑證簽署。 此程式可讓下游裝置確認閘道來自受信任的來源。

下列步驟會逐步引導您完成建立憑證的程式, 並將它們安裝在閘道上的適當位置。 您可以使用任何電腦產生憑證，然後再將其複製到您的 IoT Edge 裝置。 

## <a name="prerequisites"></a>必要條件

設定為閘道的 Azure IoT Edge 裝置。 針對下列其中一個作業系統, 請使用 IoT Edge 安裝步驟:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>使用 Windows 產生憑證

使用本節中的步驟, 在 Windows 上產生測試憑證。 您可以使用 Windows 電腦來產生憑證, 然後將它們複製到任何支援的作業系統上執行的任何 IoT Edge 裝置。 

本節中所產生的憑證是僅供測試用途。 

### <a name="install-openssl"></a>安裝 OpenSSL

在您用來產生憑證的電腦上，安裝 Windows 適用的 OpenSSL。 如果您已在 Windows 裝置上安裝 OpenSSL, 則可以略過此步驟, 但請確定您的 PATH 環境變數中可使用 OpenSSL。 

您可以透過數種方式來安裝 OpenSSL：

* **較簡便：** 下載並安裝任何[協力廠商 OpenSSL 二進位](https://wiki.openssl.org/index.php/Binaries)檔, 例如, 來自[SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/)。 將 openssl.exe 的完整路徑加入至您的 PATH 環境變數。 
   
* **建議配備：** 自行下載 OpenSSL 原始程式碼並在您的電腦上組建二進位檔，或是透過 [vcpkg](https://github.com/Microsoft/vcpkg) 來執行。 下列指示會使用 vcpkg 下載原始程式碼，並在 Windows 電腦上編譯並安裝 OpenSSL，且各項作業都能以輕鬆的步驟完成。

   1. 瀏覽至要安裝 vcpkg 的目錄。 我們將此目錄稱之為 *\<VCPKGDIR>* 。 依照指示下載並安裝 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. 安裝 vcpkg 之後, 請從 powershell 提示字元執行下列命令, 以安裝適用于 Windows x64 的 OpenSSL 套件。 安裝通常需要約 5 分鐘的時間。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. 將 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 新增至您的 PATH 環境變數，讓 openssl.exe 檔案可供叫用。

### <a name="prepare-creation-scripts"></a>準備建立指令碼

Azure IoT Edge git 存放庫包含可用來產生測試憑證的腳本。 在本節中, 您會複製 IoT Edge 存放庫並執行腳本。 

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 

2. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。 這些指令碼可協助您建立用來設定透明閘道的所需憑證。 使用 `git clone` 命令或[下載 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 瀏覽至您要使用的目錄。 在本文中, 我們將 > 呼叫此目錄 *\<WRKDIR*。 所有憑證和金鑰都會在此工作目錄中建立。

4. 將設定檔案中的設定和腳本檔案複製到您的工作目錄。 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果您將存放庫下載為 ZIP, 則資料夾名稱會是`iotedge-master` , 而且路徑的其餘部分會相同。 
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

7. 將腳本使用的函式帶入 PowerShell 的全域命名空間。
   
   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 視窗會顯示警告, 指出此腳本所產生的憑證僅供測試之用, 不應用於實際執行的案例。

8. 確認 OpenSSL 已正確安裝, 並確定不會與現有憑證產生名稱衝突。 如果有問題，指令碼應會說明如何在系統上加以修正。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>建立憑證

在本節中，您要建立三個憑證，然後將它們以鏈結方式連接。 將憑證放入鏈結檔案可讓您輕鬆地在您的 IoT Edge 閘道裝置和任何下游裝置上安裝它們。  

1. 建立根 CA 憑證, 並讓它簽署一個中繼憑證。 憑證全都放在您的工作目錄中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此指令碼命令會建立數個憑證和金鑰檔案, 但我們將在本文稍後參考其中一項:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 使用下列命令建立 IoT Edge 裝置 CA 憑證和私密金鑰。 提供 CA 憑證的名稱, 例如**MyEdgeDeviceCA**。 名稱是用來命名檔案和憑證產生期間。 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   此指令碼命令會建立數個憑證和金鑰檔, 包括我們稍後將在本文中參考的兩個檔案:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >如果您提供的名稱不是**MyEdgeDeviceCA**, 則此命令所建立的憑證和金鑰會反映該名稱。 

現在您已擁有憑證, 請直接跳到在[閘道上安裝憑證](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>使用 Linux 產生憑證

使用本節中的步驟, 在 Linux 上產生測試憑證。 您可以使用 Linux 電腦來產生憑證, 然後將它們複製到任何支援的作業系統上執行的任何 IoT Edge 裝置。 

本節中所產生的憑證是僅供測試用途。 

### <a name="prepare-creation-scripts"></a>準備建立指令碼

Azure IoT Edge git 存放庫包含可用來產生測試憑證的腳本。 在本節中, 您會複製 IoT Edge 存放庫並執行腳本。 

1. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。 這些指令碼可協助您建立用來設定透明閘道的所需憑證。 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 瀏覽至您要使用的目錄。 我們將在本文中參考此目錄, 做為 *\<WRKDIR >* 。 所有憑證和金鑰檔案都會在此目錄中建立。
  
3. 將設定檔案和腳本檔案從複製的 IoT Edge 存放庫複製到您的工作目錄。

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

   如果您已在此工作目錄中建立根和中繼憑證, 請不要再次執行此腳本。 重新執行此腳本將會覆寫現有的憑證。 相反地, 請繼續進行下一個步驟。 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此腳本會建立數個憑證和金鑰。 請記下其中一個, 我們將在下一節中參考:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. 使用下列命令建立 IoT Edge 裝置 CA 憑證和私密金鑰。 提供 CA 憑證的名稱, 例如**MyEdgeDeviceCA**。 名稱是用來命名檔案和憑證產生期間。 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   此腳本會建立數個憑證和金鑰。 請注意兩個, 我們將在下一節中參考: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >如果您提供的名稱不是**MyEdgeDeviceCA**, 則此命令所建立的憑證和金鑰會反映該名稱。 

## <a name="install-certificates-on-the-gateway"></a>在閘道上安裝憑證

既然您已經建立憑證鏈結，您需要將它安裝在 IoT Edge 閘道裝置上，並將 IoT Edge 執行階段設定為參考新的憑證。 

1. 從 *\<WRKDIR>* 複製下列檔案。 將其儲存在您 IoT Edge 裝置上的任何位置。 我們將您 IoT Edge 裝置上的目的地目錄稱之為 *\<CERTDIR>* 。 

   * 裝置 CA 憑證 -  `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * 裝置 CA 私密金鑰 - `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * 根 CA-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   您可以使用像是[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)的功能來移動憑證檔案。  如果您在 IoT Edge 裝置本身產生憑證, 可以略過此步驟, 並使用工作目錄的路徑。

2. 開啟 IoT Edge 安全性精靈組態檔。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 將 yaml 檔案中的**憑證**屬性設定為憑證的完整路徑和 IoT Edge 裝置上的金鑰檔案。 移除憑證`#`屬性前面的字元, 以取消批註四行。 請記住, yaml 中的縮排是兩個空格。

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux： 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. 在 Linux 裝置上, 請確定使用者**iotedge**具有保存憑證之目錄的 [讀取] 許可權。 

## <a name="deploy-edgehub-to-the-gateway"></a>將 Edge 中樞部署至閘道

當您第一次在裝置上安裝 IoT Edge 時, 只有一個系統模組會自動啟動: IoT Edge 代理程式。 若要讓您的裝置當作閘道運作，您需要兩個系統模組。 如果您之前尚未將任何模組部署至閘道裝置, 請為您的裝置建立初始部署, 以啟動第二個系統模組 (IoT Edge 中樞)。 部署會看起來是空的, 因為您不會在 wizard 中新增任何模組, 但它會確保兩個系統模組都在執行中。 

您可以使用 `iotedge list` 命令，檢查哪些模組是在裝置上執行。 如果清單只傳回不含**edgeHub**的模組**edgeAgent** , 請使用下列步驟:

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。

2. 移至 [IoT Edge]，並選取要作為閘道的 IoT Edge 裝置。

3. 選取 [設定模組]。

4. 選取 [下一步]。

5. 在 [指定路由] 頁面中，您應該會有可將所有模組中的所有訊息傳送至 IoT 中樞的預設路由。 如果沒有，請新增下列程式碼，然後選取 [下一步]。

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. 在 [檢閱範本] 頁面中，選取 [提交]。

## <a name="open-ports-on-gateway-device"></a>在閘道裝置上開啟埠

標準 IoT Edge 裝置不需要任何輸入連線功能, 因為與 IoT 中樞的所有通訊都是透過輸出連線來完成。 閘道裝置不同, 因為它們必須接收來自其下游裝置的訊息。 如果防火牆是在下游裝置與閘道裝置之間, 則也必須透過防火牆來進行通訊。

若要讓閘道案例正常執行, 至少必須針對來自下游裝置的輸入流量開啟其中一個 IoT Edge 中樞的支援通訊協定。 支援的通訊協定為 MQTT、AMQP 和 HTTPS。 

| 連接埠 | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>從下游裝置路由傳送訊息
IoT Edge 執行階段可以路由傳送從下游裝置送來的訊息，就像路由傳送從模組送來的訊息一樣。 這項功能可讓您在將任何資料傳送至雲端之前, 于閘道上執行的模組中執行分析。 

目前，針對下游裝置所傳送的訊息，您進行路由傳送的方式是將它們與模組所傳送的訊息做區分。 模組所傳送的訊息全都包含名為 **connectionModuleId** 系統屬性，但下游裝置所傳送的訊息則無此屬性。 您可以使用路由的 WHERE 子句來排除任何包含該系統屬性的訊息。 

下列路由是一個範例, 它會將來自任何下游裝置的訊息傳送至名`ai_insights`為的模組, `ai_insights`然後從到 IoT 中樞。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

如需有關訊息路由的詳細資訊，請參閱[部署模組及建立路由](./module-composition.md#declare-routes)。


## <a name="enable-extended-offline-operation"></a>啟用延伸離線操作

從 IoT Edge 執行時間的[1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)開始, 您可以將閘道裝置和連線到它的下游裝置設定為擴充離線作業。 

透過這項功能, 本機模組或下游裝置可以視需要重新向 IoT Edge 裝置進行驗證, 並使用訊息和方法彼此通訊, 即使與 IoT 中樞中斷連線也一樣。 如需詳細資訊，請參閱[了解適用於 IoT Edge 裝置、模組及子裝置的擴充離線功能](offline-capabilities.md)。

若要啟用擴充的離線功能, 您可以在 IoT Edge 閘道裝置和將連接的下游裝置之間建立父子式關聯性。 這些步驟會在[驗證要 Azure IoT 中樞的下游裝置](how-to-authenticate-downstream-device.md)中更詳細地說明。

## <a name="next-steps"></a>後續步驟

既然您讓 IoT Edge 裝置當作透明閘道運作，您需要將下游裝置設定為信任閘道，並將訊息傳送到該裝置。 如需詳細資訊, 請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)和[驗證下游裝置以 Azure IoT 中樞](how-to-authenticate-downstream-device.md)。
