---
title: 在 Azure HDInsight 中設定 Apache Kafka 的 SSL 加密和驗證
description: 針對 Kafka 用戶端和 Kafka 訊息代理程式之間的通訊設定 SSL 加密，以及在 Kafka 代理程式之間進行通訊。 設定用戶端的 SSL 驗證。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: d555c51838f3595367e931341a3cf6161857faef
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554596"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>在 Azure HDInsight 中設定 Apache Kafka 的安全通訊端層（SSL）加密和驗證

本文說明如何設定 Apache Kafka 用戶端與 Apache Kafka 訊息代理程式之間的 SSL 加密。 它也會說明如何設定用戶端的驗證（有時稱為雙向 SSL）。

> [!Important]
> 有兩個用戶端可供您用來 Kafka 應用程式： JAVA 用戶端和主控台用戶端。 只有 JAVA 用戶端 `ProducerConsumer.java` 可以將 SSL 用於產生和使用。 主控台產生者用戶端 `console-producer.sh` 無法與 SSL 搭配運作。

## <a name="apache-kafka-broker-setup"></a>Apache Kafka Broker 安裝程式

Kafka SSL broker 安裝程式會以下列方式使用四個 HDInsight 叢集 Vm：

* 前端節點 0-憑證授權單位單位（CA）
* 背景工作節點0、1和 2-訊息代理程式

> [!Note] 
> 本指南將會使用自我簽署憑證，但最安全的作法是使用由受信任的 CA 所核發的憑證。

Broker 安裝程式的摘要如下所示：

1. 下列步驟會在三個背景工作角色節點上重複執行：

    1. 產生憑證。
    1. 建立憑證簽署要求。
    1. 將憑證簽署要求傳送給憑證授權單位單位（CA）。
    1. 登入 CA 並簽署要求。
    1. 將已簽署的憑證傳回背景工作角色節點的 SCP。
    1. SCP CA 的公開憑證到背景工作節點。

1. 擁有所有憑證之後，請將憑證放入憑證存放區中。
1. 請移至 Ambari 並變更設定。

使用下列詳細指示來完成 broker 安裝程式：

> [!Important]
> 在下列程式碼片段中，wnX 是三個背景工作角色節點其中之一的縮寫，應以 `wn0` 取代，`wn1`，或視需要 `wn2`。 `WorkerNode0_Name` 和 `HeadNode0_Name` 應該使用個別電腦的名稱來取代，例如 `wn0-abcxyz` 或 `hn0-abcxyz`。

1. 在前端節點0上執行初始安裝，HDInsight 將會填入憑證授權單位單位（CA）的角色。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每個訊息代理程式（背景工作節點0、1和2）上執行相同的初始設定。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每個背景工作節點上，使用下面的程式碼片段執行下列步驟。
    1. 建立金鑰存放區，並將新的私人憑證填入其中。
    1. 建立憑證簽署要求。
    1. SCP 對 CA 的憑證簽署要求（headnode0）

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. 在 CA 電腦上，執行下列命令以建立 ca 憑證和 ca 金鑰檔案：

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. 變更為 CA 電腦，並簽署所有收到的憑證簽署要求：

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. 將已簽署的憑證傳回給 CA 的背景工作節點（headnode0）。

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. 將 CA 的公開憑證傳送給每個背景工作節點。

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 在每個背景工作節點上，將 CAs 公開憑證新增至 truststore 和金鑰儲存區。 然後將背景工作角色節點的已簽署憑證新增至金鑰儲存區

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>更新 Kafka 設定以使用 SSL 並重新啟動訊息代理程式

您現在已使用金鑰存放區和 truststore 來設定每個 Kafka 訊息代理程式，並已匯入正確的憑證。 接下來，請使用 Ambari 修改相關的 Kafka 設定屬性，然後重新啟動 Kafka 訊息代理程式。

若要完成設定修改，請執行以下步驟：

1. 登入 Azure 入口網站，然後選取您的 Azure HDInsight Apache Kafka 叢集。
1. 按一下 [叢集儀表板] 下方的 [Ambari 首頁] 來移至 Ambari UI。
1. 在 [Kafka Broker] \(Kafka 訊息代理程式\) 下方，將 [listeners] \(接聽程式\) 屬性設定為 `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. 在 [Advanced kafka-broker] \(進階 kafka-broker\) 下方，將 [security.inter.broker.protocol] 屬性設定為 `SSL`

    ![在 Ambari 中編輯 Kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在 [Custom kafka-broker] \(自訂 kafka-broker\) 下方，將 [ssl.client.auth] 屬性設定為 `required`。 只有當您要設定驗證和加密時，才需要執行此步驟。

    ![在 Ambari 中編輯 kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 在**Advanced kafka-env**底下，將下列幾行新增至**kafka-env template**屬性的結尾。

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![編輯 Ambari 中的 kafka-env 範本屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. 重新啟動所有 Kafka 訊息代理程式。
1. 啟動具有生產者和取用者選項的系統管理用戶端，以確認生產者和取用者都在通訊埠9093上運作。

## <a name="client-setup-with-authentication"></a>用戶端設定 (搭配驗證)

> [!Note]
> 下列步驟只有在您同時設定 SSL 加密 **和** 驗證的情況下才需要執行。 如果您只是設定加密，請繼續進行[不搭配驗證的用戶端設定](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

完成下列步驟以完成用戶端設定：

1. 登入用戶端電腦（hn1）。
1. 建立 java 金鑰儲存區並取得訊息代理程式的已簽署憑證。 然後將憑證複製到執行該 CA 的 VM。
1. 切換至 CA 電腦（hn0）以簽署用戶端憑證。
1. 移至用戶端電腦 (hn1) 並瀏覽到 `~/ssl` 資料夾。 將已簽署的憑證複製到用戶端電腦。

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

最後，使用命令 `cat client-ssl-auth.properties` 檢視檔案 `client-ssl-auth.properties`。 應該具有下列幾行：

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>用戶端設定 (不搭配驗證)

如果您不需要驗證，只設定 SSL 加密的步驟如下：

1. 登入用戶端電腦 (hn1)，並瀏覽到 `~/ssl` 資料夾
1. 將已簽署的憑證從 CA 電腦 (wn0) 複製到用戶端電腦。
1. 將 CA 憑證匯入信任存放區
1. 將 CA 憑證匯入金鑰儲存區

這些步驟如下列程式碼片段所示。

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

最後，使用命令 `cat client-ssl-auth.properties` 檢視檔案 `client-ssl-auth.properties`。 應該具有下列幾行：

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>後續步驟

* [什麼是 HDInsight 上的 Apache Kafka？](apache-kafka-introduction.md)
