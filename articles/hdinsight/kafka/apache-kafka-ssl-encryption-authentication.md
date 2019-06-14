---
title: 設定 SSL 加密及驗證適用於 Azure HDInsight 中的 Apache Kafka
description: 設定 Kafka 用戶端和 Kafka 代理程式以及之間 Kafka 代理程式之間進行通訊的 SSL 加密。 設定 SSL 驗證的用戶端。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5d567074a0038915cc43a585b34c9c71ccf3eb1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65464984"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>設定安全通訊端層 (SSL) 加密及驗證適用於 Azure HDInsight 中的 Apache Kafka

這篇文章會示範如何設定 Apache Kafka 用戶端和 Apache Kafka 代理程式之間的 SSL 加密。 它也示範如何設定驗證的用戶端 （有時稱為雙向 SSL）。

> [!Important]
> 有兩個用戶端，您可以使用 Kafka 的應用程式： Java 用戶端和主控台用戶端。 Java 用戶端`ProducerConsumer.java`可以產生和取用使用 SSL。 主控台產生者用戶端`console-producer.sh`不適用於 SSL。

## <a name="apache-kafka-broker-setup"></a>Apache Kafka 訊息代理程式安裝程式

SSL Kafka 訊息代理程式安裝程式會以下列方式使用 HDInsight 叢集的四部 Vm:

* 前端節點 0-憑證授權單位 (CA)
* 背景工作角色節點 0、 1 和 2-訊息代理程式

> [!Note] 
> 本指南將會使用自我簽署憑證，但最安全的作法是使用由受信任的 CA 所核發的憑證。

訊息代理程式安裝程序的摘要如下所示：

1. 每個 3 個背景工作節點上重複下列步驟：

    1. 產生憑證。
    1. 建立憑證簽署要求。
    1. 傳送簽署要求給憑證授權單位 (CA) 憑證。
    1. 登入的 CA，並簽署要求。
    1. SCP 回復到背景工作節點的簽署憑證。
    1. SCP CA 以背景工作角色節點的公開憑證。

1. 之後的所有憑證，將憑證放入的憑證存放區。
1. 移至 Ambari 和變更組態。

您可以使用下列的詳細的指示，完成訊息代理程式安裝程式：

> [!Important]
> 下列程式碼片段中 wnX 是其中的三個背景工作節點縮寫，應該以替代`wn0`，`wn1`或`wn2`視情況。 `WorkerNode0_Name` 並`HeadNode0_Name`應該取代的個別電腦，名稱這類`wn0-abcxyz`或`hn0-abcxyz`。

1. 前端節點 0，這針對 HDInsight 中將填入該角色的憑證授權單位 (CA) 上執行初始設定。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每個訊息代理程式 （背景工作角色節點 0、 1 和 2） 上執行相同的初始設定。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每個背景工作角色節點上執行下列步驟中使用下列程式碼片段。
    1. 建立金鑰儲存區，並填入新的私用憑證。
    1. 建立憑證簽署要求。
    1. SCP CA (headnode0) 的憑證簽署要求

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. 變更 CA 的電腦，並簽署所有收到的憑證簽署要求：

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. 將簽署的憑證傳送回背景工作角色節點中，從 CA (headnode0)。

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. 將 CA 的公開憑證傳送至每個背景工作節點。

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 每個背景工作節點上，加入 Ca 公開憑證 truststore 和金鑰儲存區。 然後將背景工作節點自己的簽署的憑證新增至金鑰儲存區

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>更新 Kafka 設定以使用 SSL 並重新啟動訊息代理程式

您現在有設定 truststore，金鑰儲存區與每個 Kafka 訊息代理程式，並匯入正確的憑證。 接下來，請使用 Ambari 修改相關的 Kafka 設定屬性，然後重新啟動 Kafka 訊息代理程式。

若要完成設定修改，請執行以下步驟：

1. 登入 Azure 入口網站，然後選取您的 Azure HDInsight Apache Kafka 叢集。
1. 按一下 [叢集儀表板]  下方的 [Ambari 首頁]  來移至 Ambari UI。
1. 在 [Kafka Broker]  \(Kafka 訊息代理程式\) 下方，將 [listeners]  \(接聽程式\) 屬性設定為 `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. 在 [Advanced kafka-broker]  \(進階 kafka-broker\) 下方，將 [security.inter.broker.protocol]  屬性設定為 `SSL`

    ![在 Ambari 中編輯 Kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在 [Custom kafka-broker]  \(自訂 kafka-broker\) 下方，將 [ssl.client.auth]  屬性設定為 `required`。 這個步驟只是如果您要設定驗證和加密所需。

    ![在 Ambari 中編輯 kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 將設定屬性新增到 Kafka `server.properties` 檔案，以公告 IP 位址而非完整網域名稱 (FQDN)。

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 若要驗證已正確進行先前的變更，您可以選擇性地檢查下列數行是否存在於 Kafka `server.properties` 檔案中。

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. 重新啟動所有 Kafka 訊息代理程式。
1. 系統管理用戶端開始生產者和取用者的選項，以確認生產者和取用者在連接埠 9093 上運作。

## <a name="client-setup-with-authentication"></a>用戶端設定 (搭配驗證)

> [!Note]
> 下列步驟只有在您同時設定 SSL 加密 **和** 驗證的情況下才需要執行。 如果您只是設定加密，請繼續進行[不搭配驗證的用戶端設定](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

完成下列步驟以完成用戶端設定：

1. 登入用戶端電腦 (hn1)。
1. 建立 java 金鑰儲存區並取得訊息代理程式的已簽署憑證。 然後將憑證複製到執行該 CA 的 VM。
1. 切換到 CA 電腦 (hn0) 來簽署用戶端憑證。
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

如果您不需要驗證，便會是設為僅 SSL 加密的步驟：

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
