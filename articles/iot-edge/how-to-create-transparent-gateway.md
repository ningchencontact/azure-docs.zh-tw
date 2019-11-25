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
ms.openlocfilehash: d0ac7fa3a1dbb1c91da5b9919bc2c62de74213b5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456791"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>設定 IoT Edge 裝置作為透明閘道

This article provides detailed instructions for configuring an IoT Edge device to function as a transparent gateway for other devices to communicate with IoT Hub. 在本文中，*IoT Edge 閘道*一詞是指當作透明閘道使用的 IoT Edge 裝置。 For more information, see [How an IoT Edge device can be used as a gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>目前狀況：
> * 已啟用 Edge 的裝置無法連線到 IoT Edge 閘道。 
> * 下游裝置無法使用檔案上傳。

There are three general steps to set up a successful transparent gateway connection. This article covers the first step:

1. **The gateway device needs to be able to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination.**
2. The downstream device needs to have a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. The downstream device needs to be able to securely connect to its gateway device. 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。


For a device to function as a gateway, it needs to be able to securely connect to its downstream devices. Azure IoT Edge 可讓您使用公開金鑰基礎結構 (PKI)，設定這些裝置之間的安全連線。 在此案例中，我們將允許下游裝置連線至作為透明閘道的 IoT Edge 裝置。 To maintain reasonable security, the downstream device should confirm the identity of the gateway device. This identity check prevents your devices from connecting to potentially malicious gateways.

A downstream device in a transparent gateway scenario can be any application or platform that has an identity created with the [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloud service. 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 就所有實際用途而言，下游裝置甚至可以是在 IoT Edge 閘道裝置本身執行的應用程式。 However, an IoT Edge device cannot be downstream of an IoT Edge gateway. 

您可以建立任何憑證基礎結構，來啟用裝置閘道拓撲所需的信任。 In this article, we assume the same certificate setup that you would use to enable [X.509 CA security](../iot-hub/iot-hub-x509ca-overview.md) in IoT Hub, which involves an X.509 CA certificate associated to a specific IoT hub (the IoT hub root CA), a series of certificates signed with this CA, and a CA for the IoT Edge device.

![閘道憑證設定](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>The term "root CA" used throughout this article refers to the topmost authority public certificate of the PKI certificate chain, and not necessarily the certificate root of a syndicated certificate authority. In many cases, it is actually an intermediate CA public certificate. 

The gateway presents its IoT Edge device CA certificate to the downstream device during the initiation of the connection. The downstream device checks to make sure the IoT Edge device CA certificate is signed by the root CA certificate. This process allows the downstream device to confirm that the gateway comes from a trusted source.

The following steps walk you through the process of creating the certificates and installing them in the right places on the gateway. 您可以使用任何電腦產生憑證，然後再將其複製到您的 IoT Edge 裝置。 

## <a name="prerequisites"></a>必要條件

* A development machine to create certificates. 
* 設定為閘道的 Azure IoT Edge 裝置。 Use the IoT Edge installation steps for one of the following operating systems:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>使用 Windows 產生憑證

Use the steps in this section to generate test certificates on Windows. You can use a Windows machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

本節中所產生的憑證是僅供測試用途。 

### <a name="install-openssl"></a>安裝 OpenSSL

在您用來產生憑證的電腦上，安裝 Windows 適用的 OpenSSL。 If you already have OpenSSL installed on your Windows device, you may skip this step, but ensure that openssl.exe is available in your PATH environment variable. 

There are several ways to install OpenSSL, including:

* **Easier:** Download and install any [third-party OpenSSL binaries](https://wiki.openssl.org/index.php/Binaries), for example, from [OpenSSL on SourceForge](https://sourceforge.net/projects/openssl/). 將 openssl.exe 的完整路徑加入至您的 PATH 環境變數。 
   
* **建議**：自行下載 OpenSSL 原始程式碼，並在您的電腦上建置二進位檔，或是透過 [vcpkg](https://github.com/Microsoft/vcpkg) 來執行。 下列指示會使用 vcpkg 下載原始程式碼，並在 Windows 電腦上編譯並安裝 OpenSSL，且各項作業都能以輕鬆的步驟完成。

   1. 瀏覽至要安裝 vcpkg 的目錄。 我們將此目錄稱之為 *\<VCPKGDIR>* 。 依照指示下載並安裝 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. Once vcpkg is installed, run the following command from a powershell prompt to install the OpenSSL package for Windows x64. 安裝通常需要約 5 分鐘的時間。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. 將 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 新增至您的 PATH 環境變數，讓 openssl.exe 檔案可供叫用。

### <a name="prepare-creation-scripts"></a>準備建立指令碼

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 

2. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。 這些指令碼會協助您建立用來設定透明閘道的所需憑證。 使用 `git clone` 命令或[下載 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 瀏覽至您要使用的目錄。 Throughout this article, we'll call this directory *\<WRKDIR>* . All certificates and keys will be created in this working directory.

4. Copy the configuration and script files from the cloned repo into your working directory. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   If you downloaded the repo as a ZIP, then the folder name is `iotedge-master` and the rest of the path is the same. 
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

7. Bring the functions used by the scripts into PowerShell's global namespace.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   The PowerShell window will display a warning that the certificates generated by this script are only for testing purposes, and should not be used in production scenarios.

8. Verify that OpenSSL has been installed correctly and make sure that there won't be name collisions with existing certificates. 如果有問題，指令碼應會說明如何在系統上加以修正。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>建立憑證

在本節中，您要建立三個憑證，然後將它們以鏈結方式連接。 將憑證放入鏈結檔案可讓您輕鬆地在您的 IoT Edge 閘道裝置和任何下游裝置上安裝它們。  

1. Create the root CA certificate and have it sign one intermediate certificate. The certificates are all placed in your working directory.

   ```powershell
   New-CACertsCertChain rsa
   ```

   This script command creates several certificate and key files, but we're going to refer to one in particular later in this article:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   This script command creates several certificate and key files, including two that we're going to refer to later in this article:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

Now that you have the certificates, skip ahead to [Install certificates on the gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>使用 Linux 產生憑證

Use the steps in this section to generate test certificates on Linux. You can use a Linux machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

本節中所產生的憑證是僅供測試用途。 

### <a name="prepare-creation-scripts"></a>準備建立指令碼

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。 這些指令碼會協助您建立用來設定透明閘道的所需憑證。 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 瀏覽至您要使用的目錄。 We'll refer to this directory throughout the article as *\<WRKDIR>* . All certificate and key files will be created in this directory.
  
3. Copy the config and script files from the cloned IoT Edge repo into your working directory.

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

1. Create the root CA certificate and one intermediate certificate. 這些憑證放置在 *\<WRKDIR>* 中。

   If you've already created root and intermediate certificates in this working directory, don't run this script again. Rerunning this script will overwrite the existing certificates. Instead, proceed to the next step. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   The script creates several certificates and keys. Make note of one, which we'll refer to in the next section:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   The script creates several certificates and keys. Make note of two, which we'll refer to in the next section: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

## <a name="install-certificates-on-the-gateway"></a>在閘道上安裝憑證

既然您已經建立憑證鏈結，您需要將它安裝在 IoT Edge 閘道裝置上，並將 IoT Edge 執行階段設定為參考新的憑證。 

1. 從 *\<WRKDIR>* 複製下列檔案。 將其儲存在您 IoT Edge 裝置上的任何位置。 我們將您 IoT Edge 裝置上的目的地目錄稱之為 *\<CERTDIR>* 。 

   * 裝置 CA 憑證 -  `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * 裝置 CA 私密金鑰 - `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Root CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate files.  If you generated the certificates on the IoT Edge device itself, you can skip this step and use the path to the working directory.

2. 開啟 IoT Edge 安全性精靈組態檔。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. Set the **certificate** properties in the config.yaml file to the full path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Remember that indents in yaml are two spaces.

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

4. On Linux devices, make sure that the user **iotedge** has read permissions for the directory holding the certificates. 

## <a name="deploy-edgehub-to-the-gateway"></a>將 Edge 中樞部署至閘道

When you first install IoT Edge on a device, only one system module starts automatically: the IoT Edge agent. 若要讓您的裝置當作閘道運作，您需要兩個系統模組。 If you haven't deployed any modules to your gateway device before, create an initial deployment for your device to start the second system module, the IoT Edge hub. The deployment will look empty because you don't add any modules in the wizard, but it will make sure that both system modules are running. 

您可以使用 `iotedge list` 命令，檢查哪些模組是在裝置上執行。 If the list only returns the module **edgeAgent** without **edgeHub**, use the following steps:

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

## <a name="open-ports-on-gateway-device"></a>Open ports on gateway device

Standard IoT Edge devices don't need any inbound connectivity to function, because all communication with IoT Hub is done through outbound connections. Gateway devices are different because they need to receive messages from their downstream devices. If a firewall is between the downstream devices and the gateway device, then communication needs to be possible through the firewall as well.

For a gateway scenario to work, at least one of the IoT Edge hub's supported protocols must be open for inbound traffic from downstream devices. The supported protocols are MQTT, AMQP, and HTTPS. 

| 連接埠 | 通訊協定 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>從下游裝置路由傳送訊息
IoT Edge 執行階段可以路由傳送從下游裝置送來的訊息，就像路由傳送從模組送來的訊息一樣。 This feature allows you to perform analytics in a module running on the gateway before sending any data to the cloud. 

目前，針對下游裝置所傳送的訊息，您進行路由傳送的方式是將它們與模組所傳送的訊息做區分。 模組所傳送的訊息全都包含名為 **connectionModuleId** 系統屬性，但下游裝置所傳送的訊息則無此屬性。 您可以使用路由的 WHERE 子句來排除任何包含該系統屬性的訊息。 

The below route is an example that would send messages from any downstream device to a module named `ai_insights`, and then from `ai_insights` to IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

如需有關訊息路由的詳細資訊，請參閱[部署模組及建立路由](./module-composition.md#declare-routes)。


## <a name="enable-extended-offline-operation"></a>Enable extended offline operation

Starting with the [v1.0.4 release](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) of the IoT Edge runtime, the gateway device and downstream devices connecting to it can be configured for extended offline operation. 

With this capability, local modules or downstream devices can re-authenticate with the IoT Edge device as needed and communicate with each other using messages and methods even when disconnected from the IoT hub. 如需詳細資訊，請參閱[了解適用於 IoT Edge 裝置、模組及子裝置的擴充離線功能](offline-capabilities.md)。

To enable extended offline capabilities, you establish a parent-child relationship between an IoT Edge gateway device and downstream devices that will connect to it. Those steps are explained in more detail in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>後續步驟

既然您讓 IoT Edge 裝置當作透明閘道運作，您需要將下游裝置設定為信任閘道，並將訊息傳送到該裝置。 Continue on to [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md) for the next steps in setting up your transparent gateway scenario. 
