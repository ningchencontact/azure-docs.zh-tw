---
title: 鏡像 Apache Kafka 主題 - Azure HDInsight
description: 了解如何使用 Apache Kafka 的鏡像功能，藉由將主題鏡像處理至次要叢集來維護 HDInsight 叢集上的 Kafka 複本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657200"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>使用 MirrorMaker，透過 HDInsight 上的 Kafka 來複寫 Apache Kafka 主題

了解如何使用 Apache Kafka 的鏡像功能，將主題複寫至次要叢集。 鏡像功能可以當作連續程序執行，或間歇地做為在叢集間移轉資料的方法。

在此範例中，會使用鏡像來複寫兩個 HDInsight 叢集之間的主題。 這兩個叢集位於不同虛擬網路中不同資料中心。

> [!WARNING]  
> 但不能將鏡像功能視為達成容錯的方法。 主題中的項目位移是主要和次要叢集之間的不同，因此用戶端無法交替使用這兩個。
>
> 如果您很擔心容錯，您應該為叢集內的主題設定複寫。 如需詳細資訊，請參閱 [開始使用 HDInsight 上的 Apache Kafka](apache-kafka-get-started.md)。

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka 鏡像的運作方式

使用鏡像適用於[MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)工具 （Apache Kafka 的一部分） 使用主要叢集上的主題中的記錄，並再建立次要叢集上的本機複本。 MirrorMaker 會使用其中一個 （或以上）*取用者*讀取從主要的叢集，以及*生產者*寫入本機 （次要） 的叢集。

最有用鏡像設定災害復原會利用不同 Azure 區域中的 Kafka 叢集。 若要達到此目的，叢集所在的虛擬網路對等互連在一起。

下圖說明鏡像程序和叢集之間通訊流動的方式：

![鏡像程序圖表](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

在主要和次要叢集可以是不同的節點數目和資料分割，以及主題中的位移也會不同。 鏡像功能會維護用於資料分割的金鑰值，因此會根據每個金鑰保留記錄順序。

### <a name="mirroring-across-network-boundaries"></a>跨網路界限鏡像

如果您需要在不同網路中的 Kafka 叢集之間進行鏡像處理，有下列額外考量︰

* **閘道**：網路必須能夠在 TCP/IP 層級進行通訊。

* **伺服器位址**:您可以選擇以解決您的叢集節點使用的 IP 位址或完整的網域名稱。

    * **IP 位址**：如果您會設定 Kafka 叢集，使用 IP 位址的公告，您可以繼續進行鏡像的安裝程式使用的代理程式節點和 zookeeper 節點的 IP 位址。
    
    * **網域名稱**:如果您不會設定 Kafka 叢集 IP 位址登廣告，叢集必須能夠彼此連線使用完整網域名稱 (Fqdn)。 這需要每個已設定成將要求轉送到其他網路的網路中的網域名稱系統 (DNS) 伺服器。 建立 Azure 虛擬網路 (而不是使用網路提供的自動 DNS) 時，您必須指定自訂 DNS 伺服器和伺服器的 IP 位址。 建立虛擬網路之後，您就必須建立使用該 IP 位址的 Azure 虛擬機器，然後在其上安裝和設定 DNS 軟體。

    > [!WARNING]  
    > 先建立和設定自訂 DNS 伺服器，然後再將 HDInsight 安裝到虛擬網路中。 HDInsight 不需要進行其他設定，即可使用針對虛擬網路設定的 DNS 伺服器。

如需有關如何連接兩個 Azure 虛擬網路的詳細資訊，請參閱[設定 VNet 對 VNet 連線](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

## <a name="mirroring-architecture"></a>鏡像架構

此架構的功能有兩個叢集的不同資源群組和虛擬網路：**主要**並**次要**。

### <a name="creation-steps"></a>建立步驟

1. 建立兩個新的資源群組：

    |資源群組 | Location |
    |---|---|
    | kafka-primary-rg | 美國中部 |
    | kafka-secondary-rg | 美國中北部 |

1. 建立新的虛擬網路**kafka 主要 vnet**中**kafka 主要 publicip01**。 保留預設設定。
1. 建立新的虛擬網路**kafka 次要 vnet**中**kafka 次要 publicip01**，也會使用預設設定。

1. 建立兩個新的 Kafka 叢集：

    | 叢集名稱 | 資源群組 | 虛擬網路 | 儲存體帳戶 |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. 建立虛擬網路對等互連。 這個步驟會建立兩個對等互連： 從**kafka 主要 vnet**要**kafka 次要 vnet**和一個從回復**kafka 次要 vnet**來**kafka 主要 vnet**。
    1. 選取  **kafka 主要 vnet**虛擬網路。
    1. 按一下 **對等互連**下方**設定**。
    1. 按一下 [新增]  。
    1. 在 **新增對等互連**畫面上，輸入詳細資料，如以下螢幕擷取畫面所示。

        ![新增 vnet 對等互連](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. 設定 IP 通告：
    1. 移至主要叢集的 Ambari 儀表板： `https://PRIMARYCLUSTERNAME.azurehdinsight.net`。
    1. 按一下  **Services** > **Kafka**。 按一下 [Configs (設定)]  索引標籤。
    1. 將下列組態程式行加入至底部**kafka env 範本**一節。 按一下 [儲存]  。
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. 輸入的附註**儲存設定**畫面，然後按一下**儲存**。
    1. 如果系統提示您使用 設定警告，按一下**仍要繼續**。
    1. 按一下  **Ok**上**儲存組態變更**。
    1. 按一下 **重新啟動** > **重新啟動所有受影響**中**需要重新啟動**通知。 按一下 **確認重新啟動所有**。

        ![重新啟動 kafka 節點](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. 設定 Kafka 在所有網路介面上接聽。
    1. 停留**Configs**索引標籤下**Services** > **Kafka**。 在  **Kafka 訊息代理程式**區段組**接聽程式**屬性設`PLAINTEXT://0.0.0.0:9092`。
    1. 按一下 [儲存]  。
    1. 按一下 **重新啟動**，並**確認重新啟動所有**。

1. 記錄訊息代理程式 IP 位址與主要叢集的 Zookeeper 位址。
    1. 按一下 **主機**Ambari 儀表板上。
    1. 請記下的 IP 位址的訊息代理程式和 Zookeeper。 代理程式節點有**wn**為前兩個字母的主機名稱，而 zookeeper 節點有**zk**作為主機名稱的前兩個字母。

        ![檢視 ip 位址](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. 針對第二個叢集重複前面三個步驟**kafka 次要叢集**： 設定 IP 通告、 設定接聽程式，並記下的訊息代理程式和 Zookeeper IP 位址。

## <a name="create-topics"></a>建立主題

1. 連接到**主要**叢集使用 SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    將 **sshuser** 替換為建立叢集時所使用的 SSH 使用者名稱。 將 **BASENAME** 替換為建立叢集時使用的基底名稱。

    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用下列命令來建立具有主要叢集的 Apache Zookeeper 主機的變數。 字串喜歡`ZOOKEEPER_IP_ADDRESS1`必須使用更早版本，記錄的實際 IP 位址取代這類`10.23.0.11`和`10.23.0.7`。 如果您使用自訂的 DNS 伺服器的 FQDN 解析，請遵循[這些步驟](apache-kafka-get-started.md#getkafkainfo)取得訊息代理程式和 zookeeper 的名稱。:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. 若要建立名為 `testtopic` 的主題，請使用下列命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. 使用下列命令確認已建立主題：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    回應包含 `testtopic`。

4. 使用下列命令檢視這的 Zookeeper 主機資訊 (**主要**) 叢集：

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    此命令會傳回類似以下文字的資訊：

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    請儲存此資訊。 此資訊使用於下一節。

## <a name="configure-mirroring"></a>設定鏡像功能

1. 連接到**次要**叢集使用不同的 SSH 工作階段：

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    將 **sshuser** 替換為建立叢集時所使用的 SSH 使用者名稱。 取代**SECONDARYCLUSTER**建立叢集時所使用的名稱。

    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. A`consumer.properties`檔案用來設定使用的通訊**主要**叢集。 若要建立檔案，請使用下列命令：

    ```bash
    nano consumer.properties
    ```

    使用下列文字做為 `consumer.properties` 檔案的內容：

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    取代**PRIMARY_ZKHOSTS** Zookeeper IP 位址，從**主要**叢集。

    此檔案會描述主要的 Kafka 叢集讀取時要使用的取用者資訊。 如需取用者組態詳細資訊，請參閱 kafka.apache.org 上的[取用者組態](https://kafka.apache.org/documentation#consumerconfigs)。

    若要儲存檔案，請使用 **Ctrl + X**、**Y** 和 **Enter** 鍵。

3. 設定通訊的產生者與次要叢集時，安裝程式的訊息代理程式 IP 位址的變數**次要**叢集。 您可以使用下列命令來建立此變數：

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    此命令`echo $SECONDARY_BROKERHOSTS`應該傳回類似下列文字的資訊：

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. A`producer.properties`檔案用來傳達**次要**叢集。 若要建立檔案，請使用下列命令：

    ```bash
    nano producer.properties
    ```

    使用下列文字做為 `producer.properties` 檔案的內容：

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    取代**SECONDARY_BROKERHOSTS**與上一個步驟中使用的訊息代理程式 IP 位址。

    如需產生者組態詳細資訊，請參閱 kafka.apache.org 上的[者組態](https://kafka.apache.org/documentation#producerconfigs)。

5. 使用下列命令來建立環境變數與次要叢集的 Zookeeper 主機的 IP 位址：

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. HDInsight 上 Kafka 的預設組態不允許自動建立主題。 您必須先使用下列其中一個選項，才能啟動鏡像程序：

    * **次要叢集上建立主題**:此選項也可讓您設定分割區數目和複寫因數。

        您可以使用下列命令提前建立主題：

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        將 `testtopic` 取代為要建立的主題名稱。

    * **設定可供自動建立主題的叢集**：此選項可讓 MirrorMaker 自動建立主題，不過它可能具有不同數目的資料分割或複寫因子的主要主題比建立它們。

        若要設定次要叢集來自動建立主題，請執行下列步驟：

        1. 移至次要叢集的 Ambari 儀表板： `https://SECONDARYCLUSTERNAME.azurehdinsight.net`。
        1. 按一下  **Services** > **Kafka**。 按一下 [Configs (設定)]  索引標籤。
        5. 在 [篩選]  欄位中，輸入 `auto.create` 的值。 這會篩選屬性清單並顯示 `auto.create.topics.enable` 設定。
        6. 將 `auto.create.topics.enable` 的值變更為 true，然後選取 [儲存]  。 新增附註，然後再次選取 [儲存]  。
        7. 依序選取 [Kafka]  服務、[重新啟動]  和 [重新啟動所有受影響的]  。 出現提示時，選取 [確認全部重新啟動]  。

        ![設定自動建立主題](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>啟動 MirrorMaker

1. 從 SSH 連線到**次要**叢集，請使用下列命令來啟動 MirrorMaker 程序：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    此範例中使用的參數：

    * **--consumer.config**：指定包含取用者屬性的檔案。 這些屬性用來建立取用者，以讀取來自*主要*Kafka 叢集。

    * **--producer.config**：指定包含產生者屬性的檔案。 這些屬性用來建立寫入生產者*次要*Kafka 叢集。

    * **--whitelist**：MirrorMaker 會從主要叢集複寫至次要資料庫的主題清單。

    * **--num.streams**：要建立的取用者執行緒數目。

    取用者的次要節點上正在等候接收訊息。

2. 從 SSH 連線到**主要**叢集，請使用下列命令來啟動產生者，並將訊息傳送至主題：

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     當您抵達有游標的空白行時，請輸入一些文字訊息。 訊息傳送至主題**主要**叢集。 完成後，使用 **Ctrl + C** 結束產生者程序。

3. 從 SSH 連線到**次要**叢集，請使用**Ctrl + C**來結束 MirrorMaker 程序。 這可能需要數秒鐘的時間才能完成程序。 若要確認訊息已複寫至次要資料庫，請使用下列命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    主題清單現在包含`testtopic`，這在 mirrormaster 將主題從主要叢集至次要資料庫時建立。 擷取主題中的訊息是您在主要叢集輸入的項目相同。

## <a name="delete-the-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

這份文件中的步驟建立不同的 Azure 資源群組中的叢集。 若要刪除所有建立的資源，您可以刪除建立的兩個資源群組： **kafka 主要 publicip01**並**kafka secondary_rg**。 刪除資源群組移除所有依照本文件中，包括叢集、 虛擬網路和儲存體帳戶中建立的資源。

## <a name="next-steps"></a>後續步驟

在本文件中，您已學會如何使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 建立 [Apache Kafka](https://kafka.apache.org/) 叢集的複本。 使用下列連結來探索使用 Kafka 的其他方式︰

* [Apache Kafka MirrorMaker 文件](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (網址為 cwiki.apache.org)。
* [開始使用 Apache Kafka on HDInsight](apache-kafka-get-started.md)
* [在 HDInsight 上搭配使用 Apache Spark 與 Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [在 HDInsight 上搭配使用 Apache Storm 與 Apache Kafka](../hdinsight-apache-storm-with-kafka.md)
* [透過 Azure 虛擬網路連線到 Apache Kafka](apache-kafka-connect-vpn-gateway.md)
