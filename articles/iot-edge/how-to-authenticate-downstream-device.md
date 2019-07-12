---
title: 驗證下游裝置-Azure IoT Edge |Microsoft Docs
description: 如何驗證下游裝置或分葉裝置到 IoT 中樞，以及其連接路由傳送到 Azure IoT Edge 閘道裝置。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082387"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>在 Azure IoT 中樞中驗證下游裝置

在透明閘道案例中，（有時稱為分葉裝置的子系） 的下游裝置會需要像任何其他裝置在 IoT 中樞的身分識別。 本文將逐步引導完成驗證下游裝置到 IoT 中樞的選項，並接著示範如何宣告的閘道連線。

有三個一般步驟來設定成功的透明閘道連線。 本文涵蓋第二個步驟：

1. 閘道裝置必須能夠安全地連接到下游裝置、 收到來自下游的裝置，並將訊息路由至適當的目的地。 如需詳細資訊，請參閱 <<c0> [ 設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。
2. **下游裝置必須具有能夠向 IoT 中樞，而且知道要透過其閘道裝置進行通訊的裝置身分識別。**
3. 下游裝置必須能夠安全地連線到其閘道裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

下游裝置可以向 IoT 中樞，使用其中一種方法： 對稱金鑰 （有時稱為 「 共用的存取金鑰 」）、 自我簽署的 X.509 憑證或 X.509 憑證授權單位 (CA) 簽署憑證。 驗證步驟是用來與 IoT 中樞，任何非 IoT Edge 裝置，但宣告閘道關聯性的小型差異所設定的步驟類似。

這篇文章中的步驟示範手動裝置佈建不自動佈建 Azure IoT 中樞裝置佈建服務。 

## <a name="symmetric-key-authentication"></a>對稱金鑰驗證

對稱金鑰的驗證或共用的存取金鑰驗證，是最簡單的方式使用 IoT 中樞進行驗證。 使用對稱金鑰的驗證，base64 金鑰是您在 IoT 中樞的 IoT 裝置識別碼相關聯。 如此一來，您的裝置可以其連接到 IoT 中樞時，您可以包含在您的 IoT 應用程式的該索引鍵。 

### <a name="create-the-device-identity"></a>建立裝置身分識別 

在您的 IoT 中樞，使用 Azure 入口網站、 Azure CLI 或 IoT 擴充功能適用於 Visual Studio Code 中加入新的 IoT 裝置。 請記住，下游裝置必須在 IoT 中樞識別為一般 IoT 裝置，不是 IoT Edge 裝置。 

當您建立新的裝置身分識別時，提供下列資訊： 

* 建立您的裝置識別碼。

* 選取 **對稱金鑰**作為驗證類型。 

* （選擇性） 選擇**設定父裝置**和選取此下游裝置會透過連線的 IoT Edge 閘道裝置。 這個步驟是選擇性的對稱金鑰的驗證，但建議因為設定父裝置可讓[離線功能](offline-capabilities.md)下游裝置。 您一律可以更新裝置詳細資料，以新增或更新版本變更父代。 

   ![使用對稱金鑰的驗證，在入口網站中建立裝置識別碼](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

您可以使用[Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension)來完成相同作業。 下列範例會建立新的 IoT 裝置與對稱金鑰驗證，並將父裝置： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

如需有關建立裝置和父子式管理的 Azure CLI 命令的詳細資訊，請參閱的參考內容[az iot 中樞裝置身分識別](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令。

### <a name="connect-to-iot-hub-through-a-gateway"></a>透過閘道連線到 IoT 中樞

相同的程序用來驗證規則的 IoT 裝置到 IoT 中樞使用對稱金鑰也會套用至下游的裝置。 唯一的差別是您要新增至閘道裝置，將連接路由傳送，或在離線案例中，以處理 IoT 中樞代表驗證的指標。 

對於對稱金鑰的驗證，沒有任何額外的步驟，您必須對針對它來驗證裝置與 IoT 中樞。 您仍然需要進行中的憑證，讓您的下游裝置可以連線到閘道裝置，如中所述[下游裝置連線至 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

在入口網站中建立的 IoT 裝置身分識別之後, 您可以擷取其主要或次要金鑰。 其中一個這些機碼必須包含您包含在任何應用程式進行通訊與 IoT 中樞連接字串中。 對稱金鑰的驗證，IoT 中樞會提供讓您方便參考格式完整的連接字串中的裝置詳細資料。 您要加入的連接字串中的閘道裝置的額外資訊。 

下游裝置的對稱金鑰的連接字串需要下列元件： 

* 裝置連線至 IoT 中樞： `Hostname={iothub name}.azure-devices.net`
* 已向中樞註冊的裝置識別碼： `DeviceID={device ID}`
* 可能的主要或次要索引鍵： `SharedAccessKey={key}`
* 裝置連線到閘道裝置。 提供**hostname** IoT Edge 閘道裝置 config.yaml 檔案中的值： `GatewayHostName={gateway hostname}`

所有在一起，完整的連接字串看起來像：

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果您建立此下游裝置的父子式關聯性，您可以藉由呼叫的閘道直接連接主機以簡化連接字串。 例如: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509 驗證 

有兩種方式，來驗證 IoT 裝置使用 X.509 憑證。 任何方式您選擇進行驗證，將裝置連接到 IoT 中樞的步驟相同。 選擇 自我簽署或 CA 簽署憑證進行驗證，然後繼續了解如何連線到 IoT 中樞。 

如需 IoT 中樞如何使用 X.509 驗證的詳細資訊，請參閱下列文章： 
* [使用 X.509 CA 憑證進行裝置驗證](../iot-hub/iot-hub-x509ca-overview.md)
* [概念性的了解 IoT 產業中的 X.509 CA 憑證](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>使用自我簽署的 X.509 憑證建立裝置身分識別

針對 X.509 自我簽署驗證，有時稱為指紋驗證，您需要建立新的憑證，以便將放在您的 IoT 裝置上。 這些憑證具有指紋，在其中您要共用使用 IoT 中樞進行驗證。 

要測試此案例的最簡單方式是使用您用來建立憑證的同一部電腦[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。 該電腦應該已可設定使用正確的工具、 根 CA 憑證和中繼 CA 憑證來建立 IoT 裝置的憑證。 您可以將複製的最終憑證及私密金鑰到下游裝置之後。 閘道文件中的步驟，您可以設定 openssl，您的電腦，然後複製的 IoT Edge 存放庫，以存取憑證的建立指令碼。 然後，您所做的工作目錄，我們稱之為 **\<WRKDIR >** 來保存的憑證。 預設的憑證是為了開發和測試，因此只有過去 30 天。 您應該已建立的根 CA 憑證和中繼憑證。 

1. 瀏覽至您在 bash 或 PowerShell 視窗中的工作目錄。 

2. 建立用於下游裝置的兩個憑證 （主要和次要）。 提供您的裝置名稱，然後是主要或次要標籤。 這項資訊用來命名檔案，以便您可以追蹤的多個裝置的憑證。 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 擷取每個憑證，也就是 40 十六進位字元字串 （稱為 IoT 中樞介面中的憑證指紋） 的 SHA1 指紋。 您可以使用下列 openssl 命令來檢視憑證，並尋找指紋：

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. 瀏覽至您的 IoT 中樞，在 Azure 入口網站中，並建立新的 IoT 裝置身分識別具有下列值： 

   * 選取 **自我簽署的 X.509**作為驗證類型。
   * 貼上您從您的裝置主要和次要憑證中複製的十六進位字串。
   * 選取 **設定父裝置**，然後選擇 IoT Edge 閘道裝置，此下游裝置會透過連線。 父裝置都需要的下游裝置的 X.509 驗證。 

   ![使用 X.509 自我簽署的驗證，在入口網站中建立裝置識別碼](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. 將下列檔案複製到任何下游裝置上的目錄：

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   您將會參考這些連線到 IoT 中樞的分葉裝置應用程式中的檔案。 您可以使用這類服務[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)或是想要的函式[安全複製協定](https://www.ssh.com/ssh/scp/)移動憑證檔案。

您可以使用[Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension)完成相同的裝置建立作業。 下列範例會建立新的 IoT 裝置與 X.509 自我簽署的驗證，並將父裝置： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

如需有關建立裝置、 憑證及父系和子系管理的 Azure CLI 命令的詳細資訊，請參閱的參考內容[az iot 中樞裝置身分識別](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令。

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>建立裝置身分識別使用 X.509 CA 簽署憑證

X.509 憑證授權單位 (CA) 簽署驗證，您需要在您用來簽署憑證，為您的 IoT 裝置的 IoT 中樞註冊的根 CA 憑證。 使用的憑證問題的根 CA 憑證或任何其中繼憑證的任何裝置都能夠進行驗證。 

這一節根據 IoT 中樞文件中詳述的指示[設定在您的 Azure IoT 中樞的 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)。 請遵循這一節以了解用來透過閘道連線的下游裝置所設定的值中的步驟。 

若要測試此案例的最簡單方式是使用您用來建立憑證的同一部電腦[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。 該電腦應該已可設定使用正確的工具、 根 CA 憑證和中繼 CA 憑證來建立 IoT 裝置的憑證。 您可以將複製的最終憑證及私密金鑰到下游裝置之後。 閘道文件中的步驟，您可以設定 openssl，您的電腦，然後複製的 IoT Edge 存放庫，以存取憑證的建立指令碼。 然後，您所做的工作目錄，我們稱之為 **\<WRKDIR >** 來保存的憑證。 預設的憑證是為了開發和測試，因此只有過去 30 天。 您應該已建立的根 CA 憑證和中繼憑證。 

1. 請依照下列中的指示[到您的 IoT 中樞註冊 X.509 CA 憑證](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)一節*設定在您的 Azure IoT 中樞的 X.509 安全性*。 在該區段中，您可以執行下列步驟： 

   1. 上傳的根 CA 憑證。 如果您使用您建立透明閘道文件中的憑證上, 傳 **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem**為根憑證檔案。 
   2. 請確認您擁有該根 CA 憑證。 您可以確認擁有使用中的憑證工具\<WRKDIR >。 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. 請依照下列中的指示[建立您的 IoT 中樞的 X.509 裝置](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)一節*設定在您的 Azure IoT 中樞的 X.509 安全性*。 在該區段中，您可以執行下列步驟： 

   1. 新增裝置。 提供的小寫名稱**裝置識別碼**，然後選擇 驗證類型**X.509 CA 簽署**。 
   2. 父裝置設定。 對於下游的裝置，請選取**設定父裝置**，然後選擇 IoT Edge 閘道裝置可連線到 IoT 中樞。 

3. 建立您的下游裝置的憑證鏈結。 使用相同的根 CA 憑證上傳到 IoT 中樞，讓這個接收鏈結。 使用您提供給您的裝置身分識別，在入口網站中的相同小寫的裝置識別碼。

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. 將下列檔案複製到任何下游裝置上的目錄： 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   您將會參考這些連線到 IoT 中樞的分葉裝置應用程式中的檔案。 您可以使用這類服務[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)或是想要的函式[安全複製協定](https://www.ssh.com/ssh/scp/)移動憑證檔案。

您可以使用[Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension)完成相同的裝置建立作業。 下列範例會建立新的 IoT 裝置與已簽署的 X.509 CA 驗證，並將父裝置： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

如需有關建立裝置和父子式管理的 Azure CLI 命令的詳細資訊，請參閱的參考內容[az iot 中樞裝置身分識別](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令。


### <a name="connect-to-iot-hub-through-a-gateway"></a>透過閘道連線到 IoT 中樞

每個 Azure IoT SDK 可處理 X.509 驗證稍有不同。 不過，相同的程序用來驗證規則的 IoT 裝置到 IoT 中樞使用 X.509 憑證也會套用至下游的裝置。 唯一的差別是您要新增至閘道裝置，將連接路由傳送，或在離線案例中，以處理 IoT 中樞代表驗證的指標。 一般情況下，您可以遵循相同的 X.509 驗證步驟，針對所有的 IoT 中樞裝置，然後只要取代的值**Hostname**為您的閘道裝置的主機名稱的連接字串中。 

下列各節示範一些不同的 SDK 語言的範例。 

>[!IMPORTANT]
>下列範例會示範 IoT 中樞 Sdk 如何使用憑證來驗證裝置。 在生產環境部署中，您應該儲存所有的祕密，例如私用或硬體安全模組 (HSM) 中的 SAS 金鑰。 

#### <a name="net"></a>.NET

如需範例的C#程式設計使用 X.509 憑證向 IoT 中樞，請參閱 <<c1> [ 設定在您的 Azure IoT 中樞的 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)。</c1> 重要程式行，該範例的一部分在此處加入是為了示範在驗證程序。

宣告您的 DeviceClient 執行個體的主機名稱，則其所使用的 IoT Edge 閘道裝置的主機名稱。 主機名稱可以找到閘道裝置的 config.yaml 檔案中。 

如果您使用 IoT Edge 的 git 存放庫所提供的測試憑證，則憑證金鑰**1234年**。

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

如需 C 程式中的範例向 IoT 中樞，使用 X.509 憑證，請參閱 C IoT SDK [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)範例。 重要程式行，該範例的一部分在此處加入是為了示範在驗證程序。

在定義您的下游裝置的連接字串時，使用 IoT Edge 閘道裝置的主機名稱**HostName**參數。 主機名稱可以找到閘道裝置的 config.yaml 檔案中。 

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

如需 Node.js 程式的範例使用 X.509 憑證向 IoT 中樞看到 Node.js IoT SDK [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)範例。 重要程式行，該範例的一部分在此處加入是為了示範在驗證程序。

在定義您的下游裝置的連接字串時，使用 IoT Edge 閘道裝置的主機名稱**HostName**參數。 主機名稱可以找到閘道裝置的 config.yaml 檔案中。 

如果您使用 IoT Edge 的 git 存放庫所提供的測試憑證，則憑證金鑰**1234年**。

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

如需 Python 程式的範例向 IoT 中樞，使用 X.509 憑證，請參閱 Java IoT SDK [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)範例。 重要程式行，該範例的一部分在此處加入是為了示範在驗證程序。

在定義您的下游裝置的連接字串時，使用 IoT Edge 閘道裝置的主機名稱**HostName**參數。 主機名稱可以找到閘道裝置的 config.yaml 檔案中。 

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

Java 程式，例如向 IoT 中樞，使用 X.509 憑證，請參閱 Java IoT SDK [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)範例。 重要程式行，該範例的一部分在此處加入是為了示範在驗證程序。

在定義您的下游裝置的連接字串時，使用 IoT Edge 閘道裝置的主機名稱**HostName**參數。 主機名稱可以找到閘道裝置的 config.yaml 檔案中。 

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

藉由完成這篇文章，您應該具備 IoT Edge 裝置作為透明閘道運作並且下游裝置向 IoT 中樞。 接下來，您需要設定下游裝置信任的閘道裝置，並將訊息傳送給它。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。
