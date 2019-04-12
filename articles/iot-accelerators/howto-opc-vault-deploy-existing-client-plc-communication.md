---
title: 安全通訊的 OPC 用戶端與 OPC 有 OPC 保存庫-Azure |Microsoft Docs
description: 簽署其使用 OPC 保存庫的 CA 的憑證，以保護 OPC 用戶端與 OPC 有進行通訊。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493981"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>安全 OPC 用戶端與 OPC 有的通訊

OPC 保存庫是微服務，可以設定、 註冊和管理憑證生命週期的 OPC UA 伺服器和雲端中的用戶端應用程式。 本文說明如何保護通訊的 OPC 用戶端與 OPC 有簽署其使用 OPC 保存庫的 CA 的憑證。

在下列設定中，OPC 用戶端測試連線到 OPC 有。 根據預設，連線不可能因為這兩個元件不會使用正確的憑證佈建。 如果未 OPC UA 元件尚未使用憑證佈建，它會產生自我簽署的憑證，在啟動。 不過，憑證可以由憑證授權單位 (CA) 簽署，而且安裝 OPC UA 元件中。 這為了 OPC 用戶端與 OPC 有後，會啟用連線。 下面的工作流程說明的程序。 OPC UA 安全性的一些背景資訊可在[這份文件](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf)白皮書 （英文）。 OPC UA 規格中，可以找到完整的資訊。

Testbed:下列環境的測試設定。

OPC 保存庫的指令碼：
- 保護通訊的 OPC 用戶端與 OPC 有簽署其使用 OPC 保存庫的 CA 的憑證。

> [!NOTE]
> 如需詳細資訊，請參閱 GitHub[存放庫](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds)。

## <a name="generate-a-self-signed-certificate-on-startup"></a>產生自我簽署的憑證，在啟動

**準備工作**

- 確保環境變數`$env:_PLC_OPT`並`$env:_CLIENT_OPT`都未定義，例如`$env:_PLC_OPT=""`在您的 PowerShell。

- 設定環境變數`$env:_OPCVAULTID`設為字串，可讓您一次 OPC 保存庫中尋找您的資料。 允許英數字元。 我們的範例，"123456"作為值，這個變數。

- 請確定沒有 docker 磁碟區`opcclient`或`opcplc`。 洽詢`docker volume ls`並將它們與移除`docker volume rm <volumename>`。 您可能需要移除也具有容器`docker rm <containerid>`如果容器仍使用磁碟區。

**快速入門**

在存放庫根目錄中執行下列 PowerShell 命令：

```
docker-compose -f connecttest.yml up
```

**驗證**

確認記錄檔中沒有任何憑證安裝在第一次啟動。 這裡 OPC 有 （類似會針對顯示 OPC 用戶端） 的記錄輸出:...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
如果您看到報告的憑證，請遵循上述的準備步驟，並刪除 docker 磁碟區。

請確認以 OPC 有連接已失敗。 您應該會看到下列輸出的 OPC 用戶端記錄輸出：

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
失敗的原因是憑證不受信任。 這表示`opc-client`嘗試連線到`opc-plc`和接收回應，指出`opc-plc`不信任`opc-client`，結果`opc-plc`無法驗證憑證，`opc-client`提供。 這是自我簽署的憑證，而不進一步的憑證設定上`opc-plc`，且因此連接失敗。

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>登入，然後將憑證安裝在 OPC UA 元件

**準備工作**
1. 查看 步驟 1 的記錄輸出，並擷取 OPC 有和 OPC 用戶端的 「 CreateSigningRequest 資訊 」。 這裡 OPC 有只顯示輸出：

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. 移至[OPC 保存庫網站](https://opcvault.azurewebsites.net/)。

1. 選取 `Register New`

1. 輸入 OPC 有資訊從記錄檔輸出`CreateSigningRequest information`輸入欄位中的區域`Register New OPC UA Application`頁面上，選取`Server`ApplicationType 為。

1. 選取 `Register`。

1. 在下一步 頁面上，`Request New Certificate for OPC UA Application`選取`Request new Certificate with Signing Request`。

1. 在下一步 頁面上，`Generate a new Certificate with a Signing Request`貼入`CSR (base64 encoded)`字串從記錄檔輸出到`CreateRequest`輸入的欄位。 請確定您複製的完整字串。

1. 選取 `Generate New Certificate`。

1. 您現在往前移動至`View Certificate Request Details`。 在此頁面上，您可以下載所有必要的資訊來佈建的憑證存放區`opc-plc`。

1. 在此頁面上：  
    - 選取 `Certificate`中`Download as Base64`中顯示的文字字串的複本和`EncodedBase64`欄位，並將其儲存供稍後使用。 我們將它稱為`<applicationcertbase64-string>`稍後。 選取 `Back`。

    - 選取 `Issuer`中`Download as Base64`中顯示的文字字串的複本和`EncodedBase64`欄位，並將其儲存供稍後使用。 我們將它稱為`<addissuercertbase64-string>`稍後。 選取 `Back`。

    - 選取 `Crl`中`Download as Base64`中顯示的文字字串的複本和`EncodedBase64`欄位，並將其儲存供稍後使用。 我們將它稱為`<updatecrlbase64-string>`稍後。 選取 `Back`。

1. 現在您的 PowerShell 中設定變數，名為`$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > 取代字串中傳遞做為選項值 Base64 字串，而您從網站擷取。

重複的開頭的完整程序`Register New`（上述步驟 3） OPC 用戶端。 有只有您需要留意下列差異：

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

**驗證**確認兩個元件現在已登入應用程式憑證。 請檢查以下的輸出的記錄輸出：

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
有應用程式憑證，並由 CA 簽署。

確認記錄檔中，現在有安裝憑證。 以下是記錄檔的輸出 OPC 有和 OPC 用戶端有類似的輸出。
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
應用程式憑證的簽發者是 CA`CN=Azure IoT OPC Vault CA, O=Microsoft Corp.`和 OPC 有信任也此 CA 所簽署的所有憑證。


確認已成功建立連線到 OPC 有 OPC 用戶端可以從 OPC 有讀取資料。 您應該會看到下列輸出的 OPC 用戶端記錄輸出：
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
如果您看到此輸出，然後 OPC 有已經信任 OPC 用戶端，反之亦然，因為兩者都有現在由 CA 簽署的憑證，並同時信任憑證的位置，所以此 CA 所簽署。

> [!NOTE] 
> 雖然我們只會針對 OPC 有示範的第一個的兩個驗證步驟，需要驗證也 OPC 用戶端。


## <a name="next-steps"></a>後續步驟

既然您已了解如何將 OPC 保存庫部署至現有的專案，以下是建議的下一個步驟：

> [!div class="nextstepaction"]
> [部署至現有的專案的 OPC 對應項](howto-opc-twin-deploy-existing.md)