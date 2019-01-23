---
title: 在 Azure HDInsight 中針對 Apache Kafka 設定 SSL 加密及驗證
description: 針對 Kafka 用戶端和 Kafka 訊息代理程式之間，以及 Kafka 訊息代理程式之間的通訊設定 SSL 加密。 設定用戶端的 SSL 驗證。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355021"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>在 Azure HDInsight 中針對 Apache Kafka 設定安全通訊端層 (SSL) 加密及驗證

本文說明如何設定從 Apache Kafka 用戶端至 Apache Kafka 訊息代理程式，以及 Apache Kafka 訊息代理程式之間的 SSL 加密。 它也會提供設定用戶端驗證 (有時被稱謂雙向 SSL) 的必要步驟。

## <a name="server-setup"></a>伺服器設定

第一個步驟是在 Kafka 訊息代理程式上設定金鑰儲存區和信任存放區，並將憑證授權單位 (CA) 和訊息代理程式憑證匯入到這些存放區中。

> [!Note] 
> 本指南將會使用自我簽署憑證，但最安全的作法是使用由受信任的 CA 所核發的憑證。

1. 建立名為 ssl 的資料夾，然後以環境變數的形式匯出伺服器密碼。 針對本指南剩餘的部分，請將 `<server_password>` 取代為伺服器實際的系統管理員密碼。

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. 接下來，請建立 java 金鑰儲存區 (kafka.server.keystore.jks) 和 CA 憑證。

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. 接著，請建立簽署要求來向 CA 簽署在上個步驟中所建立的憑證。

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. 現在，請將簽署要求傳送至 CA 以簽署此憑證。 因為我們是使用自我簽署的憑證，我們會使用 `openssl`命令來與 CA 簽署該憑證。

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. 建立信任存放區並匯入 CA 的憑證。

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. 將公用 CA 憑證匯入金鑰儲存區。

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. 將已簽署的憑證匯入金鑰儲存區。

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

將已簽署的憑證匯入金鑰儲存區，是針對 Kafka 訊息代理程式設定信任存放區和金鑰儲存區所需的最後一個步驟。

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>更新設定以使用 SSL 並重新啟動訊息代理程式

我們已針對每個 Kafka 訊息代理程式設定金鑰儲存區和信任存放區，並已匯入正確的憑證。  接下來，請使用 Ambari 修改相關的 Kafka 設定屬性，然後重新啟動 Kafka 訊息代理程式。

1. 登入 Azure 入口網站，然後選取您的 Azure HDInsight Apache Kafka 叢集。
1. 按一下 [叢集儀表板] 下方的 [Ambari 首頁] 來移至 Ambari UI。
1. 在 [Kafka Broker] \(Kafka 訊息代理程式\) 下方，將 [listeners] \(接聽程式\) 屬性設定為 `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. 在 [Advanced kafka-broker] \(進階 kafka-broker\) 下方，將 [security.inter.broker.protocol] 屬性設定為 `SSL`

    ![在 Ambari 中編輯 kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在 [Custom kafka-broker] \(自訂 kafka-broker\) 下方，將 [ssl.client.auth] 屬性設定為 `required`。 此步驟只有在您同時設定驗證和加密的情況下才需要執行。

    ![在 Ambari 中編輯 kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 將設定屬性新增到 Kafka `server.properties` 檔案，以公告 IP 位址而非完整網域名稱 (FQDN)。

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 若要驗證已正確進行先前的變更，您可以選擇性地檢查下列數行是否存在於 Kafka `server.properties` 檔案中。

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. 重新啟動所有 Kafka 訊息代理程式。

## <a name="client-setup-with-authentication"></a>用戶端設定 (搭配驗證)

> [!Note]
> 下列步驟只有在您同時設定 SSL 加密 **和** 驗證的情況下才需要執行。 如果您只是設定加密，請繼續進行[不搭配驗證的用戶端設定](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

1. 匯出用戶端密碼。 將 `<client_password>` 取代為 Kafka 用戶端電腦上實際的系統管理員密碼。

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. 建立 java 金鑰儲存區並取得訊息代理程式的已簽署憑證。 然後將憑證複製到執行該 CA 的 VM。

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. 切換到 CA 電腦 (wn0) 以簽署用戶端憑證。

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. 移至用戶端電腦 (hn1) 並瀏覽到 `~/ssl` 資料夾。 將已簽署的憑證複製到用戶端電腦。

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. 使用命令 `$cat client-ssl-auth.properties` 檢視檔案 `client-ssl-auth.properties`。 應該具有下列幾行：

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>用戶端設定 (不搭配驗證)

1. 匯出用戶端密碼。 將 `<client_password>` 取代為 Kafka 用戶端電腦上實際的系統管理員密碼。

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. 移至用戶端電腦 (hn1) 並瀏覽到 `~/ssl` 資料夾。 將已簽署的憑證複製到用戶端電腦。

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. 使用命令 `$cat client-ssl-auth.properties` 檢視檔案 `client-ssl-auth.properties`。 應該具有下列幾行：

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>後續步驟

* [什麼是 HDInsight 上的 Apache Kafka？](/../azure/hdinsight/kafka/apache-kafka-introduction)