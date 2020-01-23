---
title: 驗證下游裝置-Azure IoT Edge |Microsoft Docs
description: 如何驗證下游裝置或分葉裝置以 IoT 中樞，並透過 Azure IoT Edge 閘道裝置路由傳送其連線。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ca066729a81ff51d87c5d8063c94be86366811c
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548759"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>在 Azure IoT 中樞中驗證下游裝置

在透明閘道案例中，下游裝置（有時稱為分葉裝置或子裝置）需要 IoT 中樞的身分識別，就像任何其他裝置一樣。 本文會逐步解說用來驗證下游裝置以 IoT 中樞的選項，然後示範如何宣告閘道連線。

設定成功的透明閘道連線有三個一般步驟。 本文涵蓋第二個步驟：

1. 閘道裝置必須能夠安全地連線到下游裝置、接收來自下游裝置的通訊，以及將訊息路由傳送至適當的目的地。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以作為透明閘道](how-to-create-transparent-gateway.md)。
2. **下游裝置必須具有裝置身分識別，才能使用 IoT 中樞進行驗證，並知道要透過其閘道裝置進行通訊。**
3. 下游裝置必須安全地連接到其閘道裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

下游裝置可以使用下列三種方法的其中一種向 IoT 中樞進行驗證：對稱金鑰（有時稱為共用存取金鑰）、x.509 自我簽署憑證，或 x.509 憑證授權單位單位（CA）簽署的憑證。 驗證步驟類似于使用 IoT 中樞設定任何非 IoT Edge 裝置的步驟，但要宣告閘道關聯性的差異很小。

本文中的步驟會顯示手動裝置布建，而不是使用 Azure IoT 中樞裝置布建服務（DPS）進行自動布建。 不支援使用 DPS 布建下游裝置。

## <a name="prerequisites"></a>必要條件

完成[設定 IoT Edge 裝置以作為透明閘道](how-to-create-transparent-gateway.md)中的步驟。 如果您針對下游裝置使用 x.509 驗證，則需要使用您在透明閘道一文中設定的相同憑證產生腳本。

本文是指在數個點的*閘道主機名稱*。 閘道主機名稱會在 IoT Edge 閘道裝置上 yaml 檔案的**hostname**參數中宣告。 它在下游裝置的連接字串中稱為。 閘道主機名稱必須可解析為 IP 位址，方法是使用 DNS 或主機檔案專案。

## <a name="register-device-symmetric-key"></a>註冊裝置（對稱金鑰）

對稱金鑰驗證或共用存取金鑰驗證是使用 IoT 中樞進行驗證的最簡單方式。 使用對稱金鑰驗證時，base64 金鑰會與您在 IoT 中樞中的 IoT 裝置識別碼相關聯。 您會在 IoT 應用程式中包含該金鑰，讓您的裝置可以在連線到 IoT 中樞時呈現該金鑰。

### <a name="create-the-device-identity"></a>建立裝置身分識別

使用 Azure 入口網站、Azure CLI 或適用于 Visual Studio Code 的 IoT 擴充功能，在您的 IoT 中樞中新增 IoT 裝置。 請記住，下游裝置必須在 IoT 中樞中識別為一般 IoT 裝置，而不是 IoT Edge 裝置。

當您建立新的裝置身分識別時，請提供下列資訊：

* 建立您裝置的識別碼。

* 選取 [**對稱金鑰**] 作為 [驗證類型]。

* （選擇性）選擇 [**設定父裝置**]，然後選取此下游裝置將透過其連線的 IoT Edge 閘道裝置。 此步驟對於對稱金鑰驗證是選擇性的，但建議使用，因為設定父裝置會啟用下游裝置的[離線功能](offline-capabilities.md)。 您隨時可以更新裝置詳細資料，以在稍後新增或變更父系。

   ![在入口網站中建立具有對稱金鑰驗證的裝置識別碼](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

您可以使用[適用于 Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能來完成相同的操作。 下列範例會建立具有對稱金鑰驗證的新 IoT 裝置，並指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

如需有關建立裝置和父系/子管理的 Azure CLI 命令的詳細資訊，請參閱[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的參考內容。


接下來，抓取[並修改連接字串](#retrieve-and-modify-connection-string)，讓您的裝置知道透過其閘道連接。

## <a name="register-device-x509-self-signed"></a>註冊裝置（x.509 自我簽署）

針對 x.509 自我簽署驗證，有時也稱為指紋驗證，您必須建立新的憑證以放在您的 IoT 裝置上。 這些憑證的指紋會與您共用 IoT 中樞以進行驗證。

如果您沒有憑證授權單位單位來建立 x.509 憑證，您可以[建立示範憑證來測試 IoT Edge 的裝置功能](how-to-create-test-certificates.md)。 產生下游裝置的測試憑證時，請使用為閘道裝置產生憑證的相同根 CA 憑證。

1. 使用您的 CA 憑證，為下游裝置建立兩個裝置憑證（主要和次要）。

   裝置憑證的 [主體名稱] 必須設定為您在 Azure IoT 中樞中註冊 IoT 裝置時將使用的 [裝置識別碼]。 這是驗證所需的設定。

2. 從每個憑證（也就是40十六進位字元字串）取出 SHA1 指紋（在 IoT 中樞介面中稱為指紋）。 使用下列 openssl 命令來查看憑證並尋找指紋：

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   執行此命令兩次，一次用於主要憑證，一次用於次要憑證。 當您使用自我簽署的 x.509 憑證註冊新的 IoT 裝置時，您會為這兩個憑證提供指紋。

3. 流覽至 Azure 入口網站中的 IoT 中樞，並使用下列值建立新的 IoT 裝置身分識別：

   * 提供符合裝置憑證主體名稱的**裝置識別碼**。
   * 選取 [x.509] [**自我簽署**] 做為 [驗證類型]。
   * 貼上您從裝置的主要和次要憑證複製的十六進位字串。
   * 選取 [**設定父裝置**]，然後選擇此下游裝置將透過其連線的 IoT Edge 閘道裝置。 下游裝置的 x.509 驗證需要父裝置。

   ![在入口網站中使用 x.509 自我簽署驗證來建立裝置識別碼](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 將裝置憑證和金鑰複製到下游裝置上的任何位置。 此外，也請移動同時產生閘道裝置憑證和下游裝置憑證的共用根 CA 憑證複本。

   您將會在連接到 IoT 中樞的分葉裝置應用程式中參考這些檔案。 您可以使用像是[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)的功能來移動憑證檔案。

5. 視您偏好的語言而定，請參閱如何在 IoT 應用程式中參考 x.509 憑證的範例：

   * C#：[在您的 Azure IoT 中樞中設定 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C： [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js： [simple_sample_device_x509 .js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * JAVA： [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python： [send_message_x509 .py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

您可以使用[適用于 Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能來完成相同的裝置建立作業。 下列範例會建立具有 x.509 自我簽署驗證的新 IoT 裝置，並指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

如需有關建立裝置的 Azure CLI 命令、產生憑證，以及父和子系管理的詳細資訊，請參閱[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的參考內容。

接下來，抓取[並修改連接字串](#retrieve-and-modify-connection-string)，讓您的裝置知道透過其閘道連接。

## <a name="register-device-x509-ca-signed"></a>註冊裝置（x.509 CA 簽署）

針對 x.509 憑證授權單位單位（CA）簽署的驗證，您需要在 IoT 中樞中註冊的根 CA 憑證，以用來簽署 IoT 裝置的憑證。 任何使用由根 CA 憑證或其中繼憑證所發出之憑證的裝置，都將被允許進行驗證。

本節是根據 IoT 中樞文章在[Azure IoT 中樞中設定 x.509 安全性中](../iot-hub/iot-hub-security-x509-get-started.md)詳述的指示。 遵循本節中的步驟，以瞭解要使用哪些值來設定透過閘道連接的下游裝置。

如果您沒有憑證授權單位單位來建立 x.509 憑證，您可以[建立示範憑證來測試 IoT Edge 的裝置功能](how-to-create-test-certificates.md)。 產生下游裝置的測試憑證時，請使用為閘道裝置產生憑證的相同根 CA 憑證。

1. 請遵循在*Azure iot 中樞中設定 x.509 安全性*的[IoT 中樞註冊 x.509 CA 憑證](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)一節中的指示。 在該區段中，您會執行下列步驟：

   1. 上傳根 CA 憑證。 如果您使用的是示範憑證，根 CA 會 **\<路徑 >/certs/azure-iot-test-only.root.ca.cert.pem**。

   2. 確認您擁有該根 CA 憑證。

2. 遵循在[Azure iot 中樞中設定 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)一節中的 <<c0>為 *IoT 中樞建立 x.509 裝置一節中的指示*。 在該區段中，您會執行下列步驟：

   1. 新增裝置。 為 [**裝置識別碼**] 提供小寫的名稱，然後選擇 [ **x.509 CA 簽署**的驗證類型]。
   2. 設定父裝置。 針對下游裝置，請選取 [**設定父裝置**]，然後選擇將提供連線至 IoT 中樞的 IoT Edge 閘道裝置。

3. 建立下游裝置的憑證鏈。 使用您上傳至 IoT 中樞的相同根 CA 憑證來進行此鏈。 使用您在入口網站中為裝置身分識別所提供的相同小寫裝置識別碼。

4. 將裝置憑證和金鑰複製到下游裝置上的任何位置。 此外，也請移動同時產生閘道裝置憑證和下游裝置憑證的共用根 CA 憑證複本。

   您將會在連接到 IoT 中樞的分葉裝置應用程式中參考這些檔案。 您可以使用像是[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)的服務或類似[安全複製通訊協定](https://www.ssh.com/ssh/scp/)的功能來移動憑證檔案。

5. 視您偏好的語言而定，請參閱如何在 IoT 應用程式中參考 x.509 憑證的範例：

   * C#：[在您的 Azure IoT 中樞中設定 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C： [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js： [simple_sample_device_x509 .js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * JAVA： [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python： [send_message_x509 .py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

您可以使用[適用于 Azure CLI 的 IoT 擴充](https://github.com/Azure/azure-iot-cli-extension)功能來完成相同的裝置建立作業。 下列範例會使用 x.509 CA 簽署的驗證來建立新的 IoT 裝置，並指派父裝置：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

如需詳細資訊，請參閱[az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的 Azure CLI 參考內容。

接下來，抓取[並修改連接字串](#retrieve-and-modify-connection-string)，讓您的裝置知道透過其閘道連接。

## <a name="retrieve-and-modify-connection-string"></a>取出和修改連接字串

在入口網站中建立 IoT 裝置身分識別之後，您可以取出其主要或次要金鑰。 這些索引鍵的其中一個必須包含在應用程式用來與 IoT 中樞通訊的連接字串中。 針對對稱金鑰驗證，IoT 中樞會在裝置詳細資料中提供完整格式的連接字串，以方便您的便利性。 您需要將閘道裝置的額外資訊新增至連接字串。

下游裝置的連接字串需要下列元件：

* 裝置連接的 IoT 中樞： `Hostname={iothub name}.azure-devices.net`
* 向中樞註冊的裝置識別碼： `DeviceID={device ID}`
* 主要或次要金鑰： `SharedAccessKey={key}`
* 裝置透過連接的閘道裝置。 從 IoT Edge 閘道裝置的 yaml 檔案提供**主機名稱**值： `GatewayHostName={gateway hostname}`

總之，完整的連接字串看起來像這樣：

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果您已建立此下游裝置的父/子關聯性，則可以直接呼叫閘道做為連線主機，以簡化連接字串。 X.509 驗證需要父子式關聯性，但對於對稱金鑰驗證是選擇性的。 例如：

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

此時，您應該已註冊 IoT Edge 裝置，並將其設定為閘道。 您也有已註冊的下游 IoT 裝置，並指向其閘道裝置。 最後一個步驟是將憑證放在下游裝置上，讓它可以安全地連線到閘道。

繼續進行閘道系列中的下一篇文章，[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。

## <a name="next-steps"></a>後續步驟

完成本文之後，您應該會有一個 IoT Edge 裝置做為透明閘道，以及向 IoT 中樞註冊的下游裝置。 接下來，您必須設定下游裝置以信任閘道裝置，並安全地連線到該裝置。 如需詳細資訊，請參閱[將下游裝置連線到 Azure IoT Edge 閘道](how-to-connect-downstream-device.md)。
