---
title: 使用 Azure IoT Edge 設定下游裝置 | Microsoft Docs
description: 如何透過 Azure IoT Edge 閘道裝置設定要連線的下游或分葉裝置。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7b9993650492574fc45b7f15fa3424060079f5fe
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2018
ms.locfileid: "50915061"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>將下游裝置連線到 Azure IoT Edge 閘道

Azure IoT Edge 可實現透明閘道案例，其中一個或多個裝置可以透過維護與 IoT 中樞連線的單一閘道裝置，傳遞其訊息。 一旦您已設定閘道裝置，您需要了解如何安全地連線下游裝置。 

本文可透過下列方式，找出下游裝置連線的常見問題，並引導您設定下游裝置： 

* 說明傳輸層安全性 (TLS) 和憑證的基本概念。 
* 說明 TLS 程式庫在不同作業系統之間的運作方式，以及每個作業系統處理憑證的方式。
* 以數種語言逐步解說 Azure IoT 範例，以協助您開始使用。 

在本文中，*閘道*和 *IoT Edge 閘道*這兩個詞是指設定為透明閘道的 IoT Edge 裝置。 

## <a name="prerequisites"></a>必要條件

依照本文中的步驟進行之前，您應該有兩個裝置可供使用：

1. 設定為透明閘道的 IoT Edge 裝置。 
    [設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)

    一旦您設定閘道裝置之後，從閘道複製 **azure-iot-test-only.root.ca.cert.pem** 憑證，並在下游裝置上的任一處使用。 

2. 擁有來自 IoT 中樞之裝置身分識別的下游裝置。 
    您無法使用 IoT Edge 裝置作為下游裝置。 但是可以使用在在 IoT 中樞註冊為一般 IoT 裝置的裝置。 在入口網站中，您可以在 [IoT 裝置] 區段內註冊新的裝置。 或者，您可以使用 Azure CLI [註冊裝置](../iot-hub/quickstart-send-telemetry-c.md#register-a-device)。 複製連接字串，並讓它能夠在稍後的章節中使用。 

    目前，只有具有對稱金鑰驗證的下游裝置可以透過 IoT Edge 閘道連線。 目前不支援 X.509 憑證授權單位和 X.509 自我簽署的憑證。 

## <a name="prepare-a-downstream-device"></a>準備下游裝置

下游裝置可以是使用 [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)雲端服務建立身分識別的任何應用程式或平台。 在許多情況下，這些應用程式都會使用 [Azure IoT 裝置 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 就所有實際用途而言，下游裝置甚至可以是在 IoT Edge 閘道裝置本身執行的應用程式。 

若要將下游裝置連線到 IoT Edge 閘道，您需要具備兩樣東西：

1. 使用附加資訊的 IoT 中樞裝置連接字串設定的裝置或應用程式，可將其連線至閘道。 

    此連接字串的格式如下：`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`。 使用閘道裝置的主機名稱，將 **GatewayHostName** 屬性附加至連接字串的結尾。 **GatewayHostName** 的值在閘道裝置的 config.yaml 檔案中，應該符合 **hostname** 的值。 

    最終字串看起來像：`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`。

2. 裝置或應用程式必須信任閘道的**根 CA** 或**擁有者 CA** 憑證，才能驗證對閘道裝置的 TLS 連線。 

    本文的其餘部分將詳細說明這個更複雜的步驟。 此步驟可以透過以下其中一種方式執行：安裝作業系統憑證存放區中的 CA 憑證或 (針對某些語言) 使用的 Azure IoT SDK 參考應用程式中的憑證。

## <a name="tls-and-certificate-fundamentals"></a>TLS 和憑證的基本概念

安全地將下游裝置連線到 IoT Edge 的挑戰，如同網際網路上發生的其他任何安全用戶端/伺服器通訊。 用戶端和伺服器會透過網際網路，使用[傳輸層安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) 安全地進行通訊。 TLS 是使用標準[公開金鑰基礎結構 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 建構 (稱為憑證) 建置的。 TLS 是相當複雜的規格，而且可以處理與保護兩個端點安全相關的各種主題，但下一節簡潔地描述將裝置安全地連線到 IoT Edge 閘道所需的條件。

當用戶端連線到伺服器時，伺服器會顯示一連串的憑證，稱為*伺服器憑證鏈結*。 憑證鏈結通常會包含根憑證授權單位 (CA) 憑證、一個或多個中繼 CA 憑證，以及最後的伺服器憑證本身。 用戶端會以密碼編譯方式驗證整個伺服器憑證鏈結，藉此建立與伺服器的信任。 伺服器憑證鏈結的這個用戶端驗證稱為*伺服器驗證*。 若要驗證伺服器憑證鏈結，用戶端需要一份用來建立 (或核發) 伺服器憑證的根 CA 憑證。 一般而言，當連線到網站時，瀏覽器已預先設定常用的 CA 憑證，讓用戶端的程序順暢進行。 

當裝置連線到 Azure IoT 中樞時，該裝置就是用戶端，而 IoT 中樞雲端服務則是伺服器。 IoT 中樞雲端服務是以稱為 **Baltimore CyberTrust Root** 的根 CA 憑證作為後盾，這是公開可用且最廣泛使用的憑證。 大多數裝置上已安裝 IoT 中樞 CA 憑證，因此許多 TLS 實作 (OpenSSL、Schannel、LibreSSL) 會自動在伺服器憑證驗證期間使用該憑證。 可成功連線到 IoT 中樞的裝置在嘗試連線到 IoT Edge 閘道時可能會發生問題。

當裝置連線到 IoT Edge 閘道時，下游裝置就是用戶端，而閘道裝置則是伺服器。 但是，Azure IoT Edge 可讓操作員 (或使用者) 建置他們認為合適的閘道憑證鏈結。 操作員可以選擇使用公用 CA 憑證 (例如 Baltimore)，或使用自我簽署 (或內部) 的根 CA 憑證。 公用 CA 憑證通常會有與其相關聯的成本，因此通常用於實際執行案例中。 自我簽署的 CA 憑證慣用於開發和測試。 先決條件一節中所列的透明閘道設定文章使用自我簽署的根 CA 憑證。 

當您將自我簽署的根 CA 憑證用於 IoT Edge 閘道時，它必須安裝在或提供給嘗試連線到閘道的所有下游裝置。 

若要深入了解 IoT Edge 憑證以及一些實際執行的影響，請參閱 [IoT Edge 憑證使用方式詳細資料](iot-edge-certs.md)。

## <a name="install-certificates-using-the-os"></a>安裝使用作業系統的憑證

本文使用*擁有者 CA* 表示根 CA 憑證，因為這是閘道先決條件一文中指令碼所使用的詞彙。 

通常安裝作業系統憑證存放區中的擁有者 CA 憑證通常可讓大部分的應用程式使用擁有者 CA 憑證。 但有一些例外，例如 NodeJS 應用程式，它不是使用作業系統憑證存放區，而是使用節點執行階段的內部憑證存放區。 如果您無法安裝作業系統層級的憑證，請參閱本文稍後的語言專屬範例，以便要在應用程式中使用具有 Azure IoT SDK 的憑證。 

### <a name="ubuntu"></a>Ubuntu

下列命令是如何在 Ubuntu 主機上安裝 CA 憑證的範例。 此範例假設您將使用先決條件文章中的 **azure-iot-test-only.root.ca.cert.pem** 憑證，而且您已經將憑證複製到下游裝置上的位置。  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

您應該會看到訊息指出：「正在更新 /etc/ssl/certs 中的憑證...已新增 1 個，已移除 0 個；完成。」

### <a name="windows"></a>Windows

下列步驟是如何在 Windows 主機上安裝 CA 憑證的範例。 此範例假設您將使用先決條件文章中的 **azure-iot-test-only.root.ca.cert.pem** 憑證，而且您已經將憑證複製到下游裝置上的位置。  

1. 在 [開始] 功能表中，搜尋並選取 [管理電腦憑證]。 稱為 **certlm** 的公用程式隨即開啟。
2. 瀏覽至 [憑證 - 本機電腦] >  [受信任的根憑證授權單位]。
3. 在 [憑證] 上按一下滑鼠右鍵，然後選取 [所有工作] > [匯入]。 憑證匯入精靈應該會啟動。 
4. 請依照所指示的步驟，匯入憑證檔案 `<path>/azure-iot-test-only.root.ca.cert.pem`。 完成時，您應該會看到「已成功匯入」訊息。 

您也可以使用 .NET API，以程式設計方式安裝憑證，如本文稍後的 .NET 範例中所示。 

應用程式通常會使用 Windows 提供的 TLS 堆疊 (稱為 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel))，透過 TLS 安全地連線。 Schannel *要求*先安裝 Windows 憑證存放區中的任何憑證，然後再嘗試建立 TLS 連線。

## <a name="use-certificates-with-azure-iot-sdks"></a>使用具有 Azure IoT SDK 的憑證

本文是指當作*擁有者 CA* 的根 CA 憑證，因為這是在先決條件文章中產生自我簽署憑證之指令碼所使用的詞彙。 

本節描述如何使用簡單的範例應用程式，將 Azure IoT SDK 連線到 IoT Edge 裝置。 所有範例的目標都是將裝置用戶端連線至閘道，並對其傳送遙測訊息，然後關閉連線並結束。 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>跨所有 Azure IoT SDK 的一般概念

使用應用程式層級的範例之前，請準備好兩件事：

1. 下游裝置的 IoT 中樞連接字串已修改為指向閘道裝置。

    此連接字串的格式如下：`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`。 使用閘道裝置的主機名稱，將 **GatewayHostName** 屬性附加至連接字串的結尾。 **GatewayHostName** 的值在閘道裝置的 config.yaml 檔案中，應該符合 **hostname** 的值。 

    最終字串看起來像：`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`。

2. 您複製並儲存在下游裝置上某個位置之根 CA 憑證的完整路徑。

    例如： `<path>/azure-iot-test-only.root.ca.cert.pem`。 

### <a name="nodejs"></a>NodeJS

本節提供將 Azure IoT NodeJS 裝置用戶端連線到 IoT Edge 閘道的範例應用程式。 對於 Linux 和 Windows 主機，您必須安裝應用程式層級的根 CA 憑證，如此處所示，因為 NodeJS 應用程式不使用系統的憑證存放區。 

1. 從[適用於 Node.js 的 Azure IoT 裝置 SDK 範例存放庫](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)取得 **edge_downstream_device.js** 的範例。 
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。 
3. 在 edge_downstream_device.js 檔案中，更新 **connectionString** 和 **edge_ca_cert_path** 變數。 
4. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。 

若要了解您正在執行的範例，下列程式碼片段是用戶端 SDK 讀取憑證檔案，並將其用於建立安全 TLS 連線的方式： 

```nodejs
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

1. 從[適用於 Python 的 Azure IoT 裝置 SDK 範例](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples)取得 **edge_downstream_client** 的範例。 
2. 請檢閱 **readme.md** 檔案，以確定您擁有執行此範例的所有先決條件。 
3. 在 edge_downstream_client.py 檔案中，更新 **CONNECTION_STRING** 和 **TRUSTED_ROOT_CA_CERTIFICATE_PATH** 變數。 
4. 如需如何在裝置上執行此範例的指示，請參閱 SDK 文件。 


## <a name="test-the-gateway-connection"></a>測試閘道連線

這是範例命令，可以測試所有項目是否都已正確設定。 您應該會看到訊息指出：「驗證完成」。

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="next-steps"></a>後續步驟

了解 IoT Edge 如何將[離線功能](offline-capabilities.md)擴充到下游裝置。 