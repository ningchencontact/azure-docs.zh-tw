---
title: 連線下游裝置 - Azure IoT Edge | Microsoft Docs
description: How to configure downstream or leaf devices to connect to Azure IoT Edge gateway devices.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 719ec736fd2f28f8d8b3b226109bc988c872d10f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457131"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>將下游裝置連線到 Azure IoT Edge 閘道

This article provides instructions for establishing a trusted connection between downstream devices and IoT Edge transparent gateways. In a transparent gateway scenario, one or more devices can pass their messages through a single gateway device that maintains the connection to IoT Hub. 下游裝置可以是使用 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)雲端服務建立身分識別的任何應用程式或平台。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 A downstream device could even be an application running on the IoT Edge gateway device itself. 

There are three general steps to set up a successful transparent gateway connection. This article covers the third step:

1. The gateway device needs to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination. For more information, see [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md).
2. The downstream device needs a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **The downstream device needs to be able to securely connect to its gateway device.**

本文可透過下列方式，找出下游裝置連線的常見問題，並引導您設定下游裝置： 

* 說明傳輸層安全性 (TLS) 和憑證的基本概念。 
* 說明 TLS 程式庫在不同作業系統之間的運作方式，以及每個作業系統處理憑證的方式。
* 以數種語言逐步解說 Azure IoT 範例，以協助您開始使用。 

在本文中，*閘道*和 *IoT Edge 閘道*這兩個詞是指設定為透明閘道的 IoT Edge 裝置。 

## <a name="prerequisites"></a>必要條件 

Have the **azure-iot-test-only.root.ca.cert.pem** certificate file that was generated in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md) available on your downstream device. Your downstream device uses this certificate to validate the identity of the gateway device. 

## <a name="prepare-a-downstream-device"></a>準備下游裝置

下游裝置可以是使用 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)雲端服務建立身分識別的任何應用程式或平台。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 A downstream device could even be an application running on the IoT Edge gateway device itself. However, another IoT Edge device cannot be downstream of an IoT Edge gateway. 

>[!NOTE]
>IoT devices that have identities registered in IoT Hub can use [module twins](../iot-hub/iot-hub-devguide-module-twins.md) to isolate different process, hardware, or functions on a single device. IoT Edge gateways support downstream module connections using symmetric key authentication but not X.509 certificate authentication. 

若要將下游裝置連線到 IoT Edge 閘道，您需要具備兩樣東西：

* 使用附加資訊的 IoT 中樞裝置連接字串設定的裝置或應用程式，可將其連線至閘道。 

    This step is explained in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* The device or application has to trust the gateway's **root CA** certificate to validate the TLS connections to the gateway device. 

    This step is explained in detail in the rest of this article. This step can be performed one of two ways: by installing the CA certificate in the operating system's certificate store, or (for certain languages) by referencing the certificate within applications using the Azure IoT SDKs.

## <a name="tls-and-certificate-fundamentals"></a>TLS 和憑證的基本概念

安全地將下游裝置連線到 IoT Edge 的挑戰，如同網際網路上發生的其他任何安全用戶端/伺服器通訊。 用戶端和伺服器會透過網際網路，使用[傳輸層安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) 安全地進行通訊。 TLS 是使用標準[公開金鑰基礎結構 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 建構 (稱為憑證) 建置的。 TLS is a fairly involved specification and addresses a wide range of topics related to securing two endpoints. This section summarizes the concepts relevant for you to securely connect devices to an IoT Edge gateway.

當用戶端連線到伺服器時，伺服器會顯示一連串的憑證，稱為*伺服器憑證鏈結*。 憑證鏈結通常會包含根憑證授權單位 (CA) 憑證、一個或多個中繼 CA 憑證，以及最後的伺服器憑證本身。 用戶端會以密碼編譯方式驗證整個伺服器憑證鏈結，藉此建立與伺服器的信任。 This client validation of the server certificate chain is called *server chain validation*. The client cryptographically challenges the service to prove possession of the private key associated with the server certificate in a process called *proof of possession*. The combination of server chain validation and proof of possession is called *server authentication*. 若要驗證伺服器憑證鏈結，用戶端需要一份用來建立 (或核發) 伺服器憑證的根 CA 憑證。 一般而言，當連線到網站時，瀏覽器已預先設定常用的 CA 憑證，讓用戶端的程序順暢進行。 

當裝置連線到 Azure IoT 中樞時，該裝置就是用戶端，而 IoT 中樞雲端服務則是伺服器。 IoT 中樞雲端服務是以稱為 **Baltimore CyberTrust Root** 的根 CA 憑證作為後盾，這是公開可用且最廣泛使用的憑證。 大多數裝置上已安裝 IoT 中樞 CA 憑證，因此許多 TLS 實作 (OpenSSL、Schannel、LibreSSL) 會自動在伺服器憑證驗證期間使用該憑證。 可成功連線到 IoT 中樞的裝置在嘗試連線到 IoT Edge 閘道時可能會發生問題。

當裝置連線到 IoT Edge 閘道時，下游裝置就是用戶端，而閘道裝置則是伺服器。 但是，Azure IoT Edge 可讓操作員 (或使用者) 建置他們認為合適的閘道憑證鏈結。 操作員可以選擇使用公用 CA 憑證 (例如 Baltimore)，或使用自我簽署 (或內部) 的根 CA 憑證。 公用 CA 憑證通常會有與其相關聯的成本，因此通常用於實際執行案例中。 自我簽署的 CA 憑證慣用於開發和測試。 The transparent gateway setup articles listed in the introduction use self-signed root CA certificates. 

當您將自我簽署的根 CA 憑證用於 IoT Edge 閘道時，它必須安裝在或提供給嘗試連線到閘道的所有下游裝置。 

![閘道憑證設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

若要深入了解 IoT Edge 憑證以及一些實際執行的影響，請參閱 [IoT Edge 憑證使用方式詳細資料](iot-edge-certs.md)。

## <a name="provide-the-root-ca-certificate"></a>Provide the root CA certificate

To verify the gateway device's certificates, the downstream device needs its own copy of the root CA certificate. If you used the scripts provided in the IoT Edge git repository to create test certificates, then the root CA certificate is called **azure-iot-test-only.root.ca.cert.pem**. If you haven't already as part of the other downstream device preparation steps, move this certificate file to any directory on your downstream device. You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate file.

## <a name="install-certificates-in-the-os"></a>Install certificates in the OS

Installing the root CA certificate in the operating system's certificate store generally allows most applications to use the root CA certificate. There are some exceptions, like NodeJS applications that don't use the OS certificate store but rather use the Node runtime's internal certificate store. If you can't install the certificate at the operating system level, skip ahead to [Use certificates with Azure IoT SDKs](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

下列命令是如何在 Ubuntu 主機上安裝 CA 憑證的範例。 This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

You should see a message that says, "Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done."

### <a name="windows"></a>Windows

下列步驟是如何在 Windows 主機上安裝 CA 憑證的範例。 This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

You can install certificates using PowerShell's [Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) as an administrator:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

You can also install certificates using the **certlm** utility: 

1. 在 [開始] 功能表中，搜尋並選取 [管理電腦憑證]。 稱為 **certlm** 的公用程式隨即開啟。
2. 瀏覽至 [憑證 - 本機電腦] >  [受信任的根憑證授權單位]。
3. 在 [憑證] 上按一下滑鼠右鍵，然後選取 [所有工作] > [匯入]。 憑證匯入精靈應該會啟動。 
4. 請依照所指示的步驟，匯入憑證檔案 `<path>/azure-iot-test-only.root.ca.cert.pem`。 完成時，您應該會看到「已成功匯入」訊息。 

您也可以使用 .NET API，以程式設計方式安裝憑證，如本文稍後的 .NET 範例中所示。 

應用程式通常會使用 Windows 提供的 TLS 堆疊 (稱為 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel))，透過 TLS 安全地連線。 Schannel *要求*先安裝 Windows 憑證存放區中的任何憑證，然後再嘗試建立 TLS 連線。

## <a name="use-certificates-with-azure-iot-sdks"></a>使用具有 Azure IoT SDK 的憑證

本節描述如何使用簡單的範例應用程式，將 Azure IoT SDK 連線到 IoT Edge 裝置。 所有範例的目標都是將裝置用戶端連線至閘道，並對其傳送遙測訊息，然後關閉連線並結束。 

使用應用程式層級的範例之前，請準備好兩件事：

* Your downstream device's IoT Hub connection string modified to point to the gateway device, and any certificates required to authenticate your downstream device to IoT Hub. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* 您複製並儲存在下游裝置上某個位置之根 CA 憑證的完整路徑。

    例如： `<path>/azure-iot-test-only.root.ca.cert.pem` 。 

### <a name="nodejs"></a>NodeJS

本節提供將 Azure IoT NodeJS 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 For NodeJS applications, you must install the root CA certificate at the application level as shown here. NodeJS applications don't use the system's certificate store. 

1. 從[適用於 Node.js 的 Azure IoT 裝置 SDK 範例存放庫](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)取得 **edge_downstream_device.js** 的範例。 
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。 
3. 在 edge_downstream_device.js 檔案中，更新 **connectionString** 和 **edge_ca_cert_path** 變數。 
4. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。 

若要了解您正在執行的範例，下列程式碼片段是用戶端 SDK 讀取憑證檔案，並將其用於建立安全 TLS 連線的方式： 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

本節介紹將 Azure IoT .NET 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 不過，.NET 應用程式可以在 Linux 和 Windows 主機上，自動使用系統憑證存放區中任何已安裝的憑證。

1. 從 [IoT Edge .NET 範例資料夾](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)取得 **EdgeDownstreamDevice** 的範例。 
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。 
3. 在 **Properties / launchSettings.json** 檔案中，更新 **DEVICE_CONNECTION_STRING** 和 **CA_CERTIFICATE_PATH** 變數。 如果您想要在主機系統上使用受信任憑證存放區中安裝的憑證，請將此變數留空。 
4. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。 

若要以程式設計方式，透過 .NET 應用程式安裝憑證存放區中受信任的憑證，請參閱 **EdgeDownstreamDevice / Program.cs** 檔案中的 **InstallCACert()** 函式。 這項作業是等冪的，因此可以使用相同的值執行多次，而不會有其他影響。 

### <a name="c"></a>C

本節介紹將 Azure IoT C 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 C SDK 可以使用許多 TLS 程式庫運作，包括 OpenSSL、WolfSSL 和 Schannel。 如需詳細資訊，請參閱 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)。 

1. 從[適用於 C 的 Azure IoT 裝置 SDK 範例](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)取得 **iotedge_downstream_device_sample** 應用程式。 
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。 
3. 在 iotedge_downstream_device_sample.c 檔案中，更新 **connectionString** 和 **edge_ca_cert_path** 變數。 
4. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。 

適用於 C 的 Azure IoT 裝置 SDK 可以選擇在設定用戶端時註冊 CA 憑證。 這項作業不會在任何位置安裝憑證，而是使用記憶體中憑證的字串格式。 建立連線時，會將已儲存的憑證提供給基礎 TLS 堆疊。 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

在 Windows 主機上，如果您使用的不是 OpenSSL 或另一個 TLS 程式庫，則 SDK 會預設使用 Schannel。 若要讓 Schannel 運作，則應該安裝 Windows 憑證存放區中的 IoT Edge 根 CA 憑證，而不是使用 `IoTHubDeviceClient_SetOption` 作業設定。 

### <a name="java"></a>Java

本節介紹將 Azure IoT Java 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 

1. 從[適用於 Java 的 Azure IoT 裝置 SDK 範例](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)取得 **Send-event** 的範例。 
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。 
3. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。

### <a name="python"></a>Python

本節介紹將 Azure IoT Python 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 

1. Get the sample for **send_message** from the [Azure IoT device SDK for Python samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Ensure that you are either running in an IoT Edge container, or in a debug scenario, have the `EdgeHubConnectionString` and `EdgeModuleCACertificateFile` environment variables set.
3. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。 


## <a name="test-the-gateway-connection"></a>測試閘道連線

Use this sample command to test that your downstream device can connect to the gateway device: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

This command tests connections over MQTTS (port 8883). If you're using a different protocol, adjust the command as neccessary for AMQPS (5671) or HTTPS (433).

The output of this command may be long, including information about all the certificates in the chain. If your connection is successful, you'll see a line like `Verification: OK` or `Verify return code: 0 (ok)`.

![Verify gateway connection](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Troubleshoot the gateway connection

If your leaf device has intermittent connection to its gateway device, try the following steps for resolution. 

1. Is the gateway hostname in the connection string the same as the hostname value in the IoT Edge config.yaml file on the gateway device?
2. Is the gateway hostname resolvable to an IP Address? You can resolve intermittent connections either by using DNS or by adding a host file entry on the leaf device.
3. Are communication ports open in your firewall? Communication based on the protocol used (MQTTS:8883/AMQPS:5671/HTTPS:433) must be possible between downstream device and the transparent IoT Edge.

## <a name="next-steps"></a>後續步驟

了解 IoT Edge 如何將[離線功能](offline-capabilities.md)擴充到下游裝置。 
