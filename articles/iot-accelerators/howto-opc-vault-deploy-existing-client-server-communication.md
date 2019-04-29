---
title: 安全 OPC UA 用戶端與 OPC UA 伺服器應用程式使用 OPC 保存庫-Azure |Microsoft Docs
description: 安全 OPC UA 用戶端與 OPC UA 伺服器應用程式使用新的金鑰組與使用 OPC 保存庫的憑證。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450659"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>安全 OPC UA 用戶端與 OPC UA 伺服器應用程式 
OPC 保存庫是一項微服務，可以設定、註冊和管理雲端中的 OPC UA 伺服器和用戶端應用程式的憑證生命週期。 本文說明如何保護 OPC UA 用戶端和 OPC UA 伺服器應用程式使用新的金鑰組與使用 OPC 保存庫的憑證。

在下列設定中，OPC 用戶端正在測試 OPC 有的連線。 根據預設，連線不可能因為這兩個元件具有尚未佈建正確的憑證。 在此工作流程中，我們請勿使用 OPC UA 元件的自我簽署的憑證並簽署它們透過 OPC 保存庫。 請參閱先前[testbed](howto-opc-vault-deploy-existing-client-plc-communication.md)。 相反地，此 testbed 會佈建兩者所產生 OPC 保存庫的元件使用新的憑證，以及使用新的私密金鑰。 OPC UA 安全性的一些背景資訊可在這[（英文） 白皮書](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf)。 OPC UA 規格中，可以找到完整的資訊。

Testbed:下列環境的測試設定。

OPC 保存庫的指令碼：
- 安全 OPC UA 用戶端與 OPC UA 伺服器應用程式使用新的金鑰組與使用 OPC 保存庫的憑證。

> [!NOTE]
> 如需詳細資訊，請參閱 GitHub[存放庫](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds)。

## <a name="generate-a-new-certificate-and-private-key"></a>產生新的憑證和私密金鑰 
**準備**
- 請確認環境變數`$env:_PLC_OPT`和`$env:_CLIENT_OPT`為未定義。 比方說，`$env:_PLC_OPT=""`在您的 PowerShell
- 設定環境變數`$env:_OPCVAULTID`設為字串，可讓您一次 OPC 保存庫中尋找您的資料。 我們建議將它設定為 6 位數的數字。 我們的範例，"123456"用於做為值的變數。
- 請確定沒有 docker 磁碟區`opcclient`或`opcplc`。 洽詢`docker volume ls`並將它們與移除`docker volume rm <volumename>`。 您可能需要移除也具有容器`docker rm <containerid>`如果容器仍使用磁碟區。

**快速入門**
1. 移至[OPC 保存庫的網站](https://opcvault.azurewebsites.net/)

1. 選取 `Register New`

1. 輸入 OPC 有資訊，它已在先前的 testbed 記錄輸出中所示`CreateSigningRequest information`輸入欄位中的區域`Register New OPC UA Application`頁面上，選取`Server`ApplicationType 為。

1. 選取 `Register`

1. 在下一步 頁面上，`Request New Certificate for OPC UA Application`選取 `Request new KeyPair and Certificate`

1. 在下一步 頁面上，`Generate a new Certificate with a Signing Request`貼入`CSR (base64 encoded)`字串從記錄檔輸出到`CreateRequest`輸入的欄位。 請確定您複製的完整字串。

1. 在下一步 頁面上，`Request New Certificate for OPC UA Application`選取 `Request new Certificate with Signing Request`

1. 下一個頁面上`Generate a new KeyPair and for an OPC UA Application`輸入`CN=OpcPlc`SubjectName，作為`opcplc-<_OPCVAULTID>`(取代`<_OPCVAULTID>`與您的記錄) 網域名稱，選取`PEM`PrivateKeyFormat 為並輸入密碼 (我們稍後將它稱為`<certpassword-string>`)

1. 選取 `Generate New KeyPair`

1. 您現在往前移動至`View Certificate Request Details`。 在此頁面上，您可以下載所有必要的資訊來佈建的憑證存放區`opc-plc`。

1. 在此頁面上：  
    - 選取 `Certificate`中`Download as Base64`中顯示的文字字串的複本和`EncodedBase64`欄位，並將其儲存供稍後使用。 我們將它稱為`<applicationcertbase64-string>`稍後。 選取 `Back`。
    - 選取 `PrivateKey`中`Download as Base64`中顯示的文字字串的複本和`EncodedBase64`欄位，並將其儲存供稍後使用。 我們將它稱為`<privatekeybase64-string>`稍後。 選取 `Back`。
    - 選取 `Issuer`中`Download as Base64`中顯示的文字字串的複本和`EncodedBase64`欄位，並將其儲存供稍後使用。 我們將它稱為`<addissuercertbase64-string>`稍後。 選取 `Back`。
    - 選取 `Crl`中`Download as Base64`中顯示的文字字串的複本和`EncodedBase64`欄位，並將其儲存供稍後使用。 我們將它稱為`<updatecrlbase64-string>`稍後。 選取 `Back`。

1. 現在您的 PowerShell 中設定變數，名為`$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    取代字串中傳遞做為選項值 Base64 字串，而您從網站擷取。  

1. 重複的開頭的完整程序`Register New`OPC 用戶端。 有只有您需要留意下列差異：
    - 使用的記錄輸出`opcclient`。
    - 選取`Client`為 ApplicationType 在註冊期間。
    - 使用`$env:_CLIENT_OPT`做為 PowerShell 變數的名稱。

    > [!NOTE] 
    > 同時使用這種情況下，您可能會有可辨識`<addissuercertbase64-string>`和`<updatecrlbase64-string>`的值為相同`opcplc`和`opcclient`。 這是針對我們的使用案例，則為 true，而且可以為您節省一些時間，同時執行的步驟。

**快速入門**

在存放庫根目錄中執行下列 PowerShell 命令：

```
docker-compose -f connecttest.yml up
```

**驗證**

確認兩個元件未有現有的應用程式憑證。 請檢查記錄檔輸出。 以下是 OPC 有的輸出和 OPC 用戶端有類似的記錄檔輸出。

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
如果沒有應用程式憑證，移除 docker 磁碟區，在準備步驟中所述。

確認記錄檔中的 OPC 保存庫的 CA 憑證已安裝的簽發者憑證存放區也與受信任的對等憑證存放區。 以下是記錄檔的輸出 OPC 有和 OPC 用戶端有類似的記錄檔輸出。 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
OPC 有現在未使用 OPC 保存庫所簽署的憑證信任所有 OPC UA 用戶端。

私密金鑰格式視為 PEM 且已安裝新的應用程式憑證，請確認記錄檔中。 以下是記錄檔的輸出 OPC 有和 OPC 用戶端有類似的記錄檔輸出。 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

應用程式憑證和私密金鑰是現在應用程式的憑證存放區中安裝及使用的 OPC UA 應用程式。

請確認 OPC 用戶端與 OPC 有之間的連線可以成功建立 OPC 用戶端可以成功地從 OPC 有讀取資料。 您應該會看到下列輸出的 OPC 用戶端記錄輸出：
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
如果您看到此輸出，然後 OPC 有現在信任 OPC 用戶端，反之亦然，因為兩者都有現在由 CA 和這兩種信任憑證所簽署此 CA 所簽署的憑證。

### <a name="a-testbed-for-opc-publisher"></a>OPC 發行者 testbed ###

**快速入門**

在存放庫根目錄中執行下列 PowerShell 命令：
```
docker-compose -f testbed.yml up
```

**驗證**
- 確認資料會傳送至您設定的設定 IoTHub`_HUB_CS`使用[Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)或是[iothub explorer](https://github.com/Azure/iothub-explorer)。
- OPC 測試用戶端會使用 IoTHub 直接方法呼叫和 OPC 方法呼叫來設定 OPC 發行者發佈/取消發行 OPC 測試伺服器的節點。
- 監看有錯誤訊息輸出。

## <a name="next-steps"></a>後續步驟

既然您已了解如何將 OPC 保存庫部署至現有的專案，以下是建議的下一個步驟：

> [!div class="nextstepaction"]
> [部署至現有的專案的 OPC 對應項](howto-opc-twin-deploy-existing.md)