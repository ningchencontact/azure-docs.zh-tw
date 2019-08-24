---
title: 驗證下游裝置-Azure IoT Edge |Microsoft Docs
description: 如何驗證下游裝置或分葉裝置以 IoT 中樞, 並透過 Azure IoT Edge 閘道裝置路由傳送其連線。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1634d7cd3dfe8d118e220fa8620ef6467c15ea2c
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983011"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>在 Azure IoT 中樞中驗證下游裝置

在透明閘道案例中, 下游裝置 (有時稱為分葉裝置或子裝置) 需要 IoT 中樞的身分識別, 就像任何其他裝置一樣。 本文會逐步解說用來驗證下游裝置以 IoT 中樞的選項, 然後示範如何宣告閘道連線。

設定成功的透明閘道連線有三個一般步驟。 本文涵蓋第二個步驟:

1. 閘道裝置必須能夠安全地連線到下游裝置、接收來自下游裝置的通訊, 以及將訊息路由傳送至適當的目的地。 如需詳細資訊, 請參閱[設定 IoT Edge 裝置以作為透明閘道](how-to-create-transparent-gateway.md)。
2. **下游裝置必須具有裝置身分識別, 才能使用 IoT 中樞進行驗證, 並知道要透過其閘道裝置進行通訊。**
3. 下游裝置必須能夠安全地連接到其閘道裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

下游裝置可以使用下列三種方法的其中一種向 IoT 中樞進行驗證: 對稱金鑰 (有時稱為共用存取金鑰)、x.509 自我簽署憑證, 或 x.509 憑證授權單位單位 (CA) 簽署的憑證。 驗證步驟類似于使用 IoT 中樞設定任何非 IoT Edge 裝置的步驟, 但要宣告閘道關聯性的差異很小。

本文中的步驟會顯示手動裝置布建, 而不是使用 Azure IoT 中樞裝置布建服務進行自動布建。 

## <a name="prerequisites"></a>必要條件

完成[設定 IoT Edge 裝置以作為透明閘道](how-to-create-transparent-gateway.md)中的步驟。

本文是指在數個點的*閘道主機名稱*。 閘道主機名稱會在 IoT Edge 閘道裝置上 yaml 檔案的**hostname**參數中宣告。 它是用來建立本文中的憑證, 而在下游裝置的連接字串中則稱為。 閘道主機名稱必須可解析為 IP 位址, 方法是使用 DNS 或主機檔案專案。

## <a name="symmetric-key-authentication"></a>對稱金鑰驗證

對稱金鑰驗證或共用存取金鑰驗證是使用 IoT 中樞進行驗證的最簡單方式。 使用對稱金鑰驗證時, base64 金鑰會與您在 IoT 中樞中的 IoT 裝置識別碼相關聯。 您會在 IoT 應用程式中包含該金鑰, 讓您的裝置可以在連線到 IoT 中樞時呈現該金鑰。 

### <a name="create-the-device-identity"></a>建立裝置身分識別 

使用 Azure 入口網站、Azure CLI 或適用于 Visual Studio Code 的 IoT 擴充功能, 在您的 IoT 中樞中新增 IoT 裝置。 請記住, 下游裝置必須在 IoT 中樞中識別為一般 IoT 裝置, 而不是 IoT Edge 裝置。 

當您建立新的裝置身分識別時, 請提供下列資訊: 

* 建立您裝置的識別碼。

* 選取 [**對稱金鑰**] 作為 [驗證類型]。 

* (選擇性) 選擇 [**設定父裝置**], 然後選取此下游裝置將透過其連線的 IoT Edge 閘道裝置。 此步驟對於對稱金鑰驗證是選擇性的, 但建議使用, 因為設定父裝置會啟用下游裝置的[離線功能](offline-capabilities.md)。 您隨時可以更新裝置詳細資料, 以在稍後新增或變更父系。 

   ![在入口網站中建立具有對稱金鑰驗證的裝置識別碼](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

您可以使用[適用于 Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能來完成相同的操作。 下列範例會建立具有對稱金鑰驗證的新 IoT 裝置, 並指派父裝置: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

如需有關建立裝置和父系/子管理的 Azure CLI 命令的詳細資訊, 請參閱[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的參考內容。

### <a name="connect-to-iot-hub-through-a-gateway"></a>透過閘道連接到 IoT 中樞

相同的程式是用來驗證一般 IoT 裝置, 以對稱金鑰的 IoT 中樞也適用于下游裝置。 唯一的差別在於, 您需要新增閘道裝置的指標以路由連線, 或在離線案例中, 代表 IoT 中樞來處理驗證。 

針對對稱金鑰驗證, 您不需要在裝置上採取額外的步驟, 就能使用 IoT 中樞進行驗證。 您仍然需要備妥憑證, 讓下游裝置可以連線到其閘道裝置, 如[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)中所述。

在入口網站中建立 IoT 裝置身分識別之後, 您可以取出其主要或次要金鑰。 其中一個索引鍵必須包含在連接字串中, 而您包含在與 IoT 中樞通訊的任何應用程式中。 針對對稱金鑰驗證, IoT 中樞會在裝置詳細資料中提供完整格式的連接字串, 以方便您的便利性。 您需要將閘道裝置的額外資訊新增至連接字串。 

下游裝置的對稱金鑰連接字串需要下列元件: 

* 裝置連接的 IoT 中樞:`Hostname={iothub name}.azure-devices.net`
* 向中樞註冊的裝置識別碼:`DeviceID={device ID}`
* 主要或次要金鑰:`SharedAccessKey={key}`
* 裝置透過連接的閘道裝置。 從 IoT Edge 閘道裝置的 yaml 檔案提供**主機名稱**值:`GatewayHostName={gateway hostname}`

總之, 完整的連接字串看起來像這樣:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果您已建立此下游裝置的父/子關聯性, 則可以直接呼叫閘道做為連線主機, 以簡化連接字串。 例如: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509 authentication 

有兩種方式可使用 x.509 憑證來驗證 IoT 裝置。 無論您選擇驗證的方式為何, 將裝置連線至 IoT 中樞的步驟都相同。 選擇自我簽署或 CA 簽署憑證以進行驗證, 然後繼續瞭解如何連接到 IoT 中樞。 

如需 IoT 中樞如何使用 x.509 authentication 的詳細資訊, 請參閱下列文章: 
* [使用 x.509 CA 憑證進行裝置驗證](../iot-hub/iot-hub-x509ca-overview.md)
* [概念瞭解 x.509 IoT 產業中的 CA 憑證](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>使用 x.509 自我簽署憑證建立裝置身分識別

針對 x.509 自我簽署驗證, 有時也稱為指紋驗證, 您必須建立新的憑證以放在您的 IoT 裝置上。 這些憑證的指紋會與您共用 IoT 中樞以進行驗證。 

測試此案例最簡單的方式是使用您在設定 IoT Edge 裝置中用來建立憑證的相同電腦,[以作為透明閘道](how-to-create-transparent-gateway.md)。 該機器應該已經使用正確的工具、根 CA 憑證和中繼 CA 憑證進行設定, 以建立 IoT 裝置憑證。 之後, 您可以將最終的憑證及其私密金鑰複製到下游裝置。 遵循閘道一文中的步驟, 您可以在電腦上設定 openssl, 然後複製 IoT Edge 存放庫來存取憑證建立腳本。 然後, 您建立了一個工作目錄, 我們稱之為 **\<WRKDIR >** 用來保存憑證。 預設憑證適用于開發和測試, 因此只有過去30天。 您應該已建立根 CA 憑證和中繼憑證。 

1. 在 bash 或 PowerShell 視窗中流覽至您的工作目錄。 

2. 建立下游裝置的兩個憑證 (主要和次要)。 提供您的裝置名稱, 然後是主要或次要標籤。 這項資訊是用來命名檔案, 讓您可以追蹤多個裝置的憑證。 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 從每個憑證 (也就是40十六進位字元字串) 取出 SHA1 指紋 (在 IoT 中樞介面中稱為指紋)。 使用下列 openssl 命令來查看憑證並尋找指紋:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. 流覽至 Azure 入口網站中的 IoT 中樞, 並使用下列值建立新的 IoT 裝置身分識別: 

   * 選取 [x.509] [**自我簽署**] 做為 [驗證類型]。
   * 貼上您從裝置的主要和次要憑證複製的十六進位字串。
   * 選取 [**設定父裝置**], 然後選擇此下游裝置將透過其連線的 IoT Edge 閘道裝置。 下游裝置的 x.509 驗證需要父裝置。 

   ![在入口網站中使用 x.509 自我簽署驗證來建立裝置識別碼](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. 將下列檔案複製到下游裝置上的任何目錄:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   您將會在連接到 IoT 中樞的分葉裝置應用程式中參考這些檔案。 您可以使用像是[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)的功能來移動憑證檔案。

您可以使用[適用于 Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能來完成相同的裝置建立作業。 下列範例會建立具有 x.509 自我簽署驗證的新 IoT 裝置, 並指派父裝置: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

如需有關建立裝置的 Azure CLI 命令、產生憑證, 以及父和子系管理的詳細資訊, 請參閱[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的參考內容。

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>使用 x.509 CA 簽署的憑證來建立裝置身分識別

針對 x.509 憑證授權單位單位 (CA) 簽署的驗證, 您需要在 IoT 中樞中註冊的根 CA 憑證, 以用來簽署 IoT 裝置的憑證。 任何使用由根 CA 憑證或其中繼憑證所發出之憑證的裝置, 都將被允許進行驗證。 

本節是根據 IoT 中樞文章在[Azure IoT 中樞中設定 x.509 安全性中](../iot-hub/iot-hub-security-x509-get-started.md)詳述的指示。 遵循本節中的步驟, 以瞭解要使用哪些值來設定透過閘道連接的下游裝置。 

測試此案例最簡單的方式, 就是使用您在設定 IoT Edge 裝置中用來建立憑證的相同電腦,[以作為透明閘道](how-to-create-transparent-gateway.md)。 該機器應該已經使用正確的工具、根 CA 憑證和中繼 CA 憑證進行設定, 以建立 IoT 裝置憑證。 之後, 您可以將最終的憑證及其私密金鑰複製到下游裝置。 遵循閘道一文中的步驟, 您可以在電腦上設定 openssl, 然後複製 IoT Edge 存放庫來存取憑證建立腳本。 然後, 您建立了一個工作目錄, 我們稱之為 **\<WRKDIR >** 用來保存憑證。 預設憑證適用于開發和測試, 因此只有過去30天。 您應該已建立根 CA 憑證和中繼憑證。 

1. 請遵循在*Azure iot 中樞中設定 x.509 安全性*的[IoT 中樞註冊 x.509 CA 憑證](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)一節中的指示。 在該區段中, 您會執行下列步驟: 

   1. 上傳根 CA 憑證。 如果您使用的是在透明閘道一文中建立的憑證, 請將 **\<WRKDIR >/certs/azure-iot-test-only.root.ca.cert.pem**上傳為根憑證檔案。 
   2. 確認您擁有該根 CA 憑證。 您可以在 WRKDIR > 中\<驗證是否擁有憑證工具。 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. 遵循在[Azure iot 中樞中設定 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)一節中的 <<c0>為 *IoT 中樞建立 x.509 裝置一節中的指示*。 在該區段中, 您會執行下列步驟: 

   1. 新增裝置。 為 [**裝置識別碼**] 提供小寫的名稱, 然後選擇 [ **x.509 CA 簽署**的驗證類型]。 
   2. 設定父裝置。 針對下游裝置, 請選取 [**設定父裝置**], 然後選擇將提供連線至 IoT 中樞的 IoT Edge 閘道裝置。 

3. 建立下游裝置的憑證鏈。 使用您上傳至 IoT 中樞的相同根 CA 憑證來進行此鏈。 使用您在入口網站中為裝置身分識別所提供的相同小寫裝置識別碼。

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. 將下列檔案複製到下游裝置上的任何目錄: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   您將會在連接到 IoT 中樞的分葉裝置應用程式中參考這些檔案。 您可以使用像是[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)的功能來移動憑證檔案。

您可以使用[適用于 Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能來完成相同的裝置建立作業。 下列範例會使用 x.509 CA 簽署的驗證來建立新的 IoT 裝置, 並指派父裝置: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

如需有關建立裝置和父系/子管理的 Azure CLI 命令的詳細資訊, 請參閱[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的參考內容。


### <a name="connect-to-iot-hub-through-a-gateway"></a>透過閘道連接到 IoT 中樞

每個 Azure IoT SDK 會以不同的方式處理 x.509 驗證。 不過, 相同的程式是用來驗證使用 x.509 的一般 IoT 裝置, 以 IoT 中樞。憑證也適用于下游裝置。 唯一的差別在於, 您需要新增閘道裝置的指標以路由連線, 或在離線案例中, 代表 IoT 中樞來處理驗證。 一般來說, 您可以針對所有 IoT 中樞裝置遵循相同的 x.509 authentication 步驟, 然後直接將連接字串中的 [**主機名稱**] 的值取代為閘道裝置的主機名稱。 

下列各節顯示不同 SDK 語言的一些範例。 

>[!IMPORTANT]
>下列範例示範 IoT 中樞 Sdk 如何使用憑證來驗證裝置。 在生產部署中, 您應該將所有秘密 (例如私人或 SAS 金鑰) 儲存在硬體安全模組 (HSM) 中。 

#### <a name="net"></a>.NET

如需驗證 x.509 憑證C# IoT 中樞的程式範例, 請參閱[在您的 Azure IoT 中樞中設定 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)。 這裡包含該範例的一些重要行, 以示範驗證程式。

為您的 DeviceClient 實例宣告主機名稱時, 請使用 IoT Edge 閘道裝置的主機名稱。 主機名稱可以在閘道裝置的 yaml 檔案中找到。 

如果您使用 IoT Edge git 存放庫所提供的測試憑證, 憑證的金鑰就是**1234**。

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

如需使用 x.509 憑證向 IoT 中樞驗證的 C 程式範例, 請參閱 C IoT SDK 的[iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)範例。 這裡包含該範例的一些重要行, 以示範驗證程式。

定義下游裝置的連接字串時, 請使用 IoT Edge 閘道裝置的主機名稱做為**主機名稱**參數。 主機名稱可以在閘道裝置的 yaml 檔案中找到。 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

如需使用 x.509 憑證向 IoT 中樞進行驗證的 node.js 程式範例, 請參閱 node.js IoT SDK 的[simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)範例。 這裡包含該範例的一些重要行, 以示範驗證程式。

定義下游裝置的連接字串時, 請使用 IoT Edge 閘道裝置的主機名稱做為**主機名稱**參數。 主機名稱可以在閘道裝置的 yaml 檔案中找到。 

如果您使用 IoT Edge git 存放庫所提供的測試憑證, 憑證的金鑰就是**1234**。

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

如需使用 x.509 憑證向 IoT 中樞進行驗證的 Python 程式範例, 請參閱 JAVA IoT SDK 的[iothub_client_sample_x509. .py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)範例。 這裡包含該範例的一些重要行, 以示範驗證程式。

定義下游裝置的連接字串時, 請使用 IoT Edge 閘道裝置的主機名稱做為**主機名稱**參數。 主機名稱可以在閘道裝置的 yaml 檔案中找到。 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)


def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

如需使用 x.509 憑證驗證 IoT 中樞的 JAVA 程式範例, 請參閱 JAVA IoT SDK 的[SendEventX509](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)範例。 這裡包含該範例的一些重要行, 以示範驗證程式。

定義下游裝置的連接字串時, 請使用 IoT Edge 閘道裝置的主機名稱做為**主機名稱**參數。 主機名稱可以在閘道裝置的 yaml 檔案中找到。 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>後續步驟

完成本文之後, 您應該會有一個 IoT Edge 裝置做為透明閘道, 以及向 IoT 中樞註冊的下游裝置。 接下來, 您必須設定下游裝置以信任閘道裝置, 並將訊息傳送給它。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。
