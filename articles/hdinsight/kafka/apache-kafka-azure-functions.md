---
title: 使用 Azure Functions 將資料傳送到 Kafka on HDInsight | Microsoft Docs
description: 了解如何使用 Azure Functions 將資料寫入至 Kafka on HDInsight。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: larryfr
ms.openlocfilehash: 6c462f9fe5e152c82be1a2b8643ee35d260a90f6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>從 Azure 函式應用程式使用 Kafka on HDInsight

了解如何建立 Azure 函式應用程式，以將資料傳送至 Kafka on HDInsight。

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 是無伺服器計算服務。 它可讓您依需求執行程式碼，無需明確佈建或管理基礎結構。

[Apache Kafka](https://kafka.apache.org) 是開放原始碼分散式串流平台，可用來建置即時串流資料管線和應用程式。 Kafka 也提供類似於訊息佇列的訊息代理程式功能，可讓您發佈和訂閱具名資料流。 在 HDInsight 上的 Kafka 為您在 Microsoft Azure 雲端中提供受控、高可調整性和高可用性的服務。

Kafka on HDInsight 不會在公用網際網路上提供 API。 若要從 Kafka 發佈或取用資料，您必須使用 Azure 虛擬網路連線到 Kafka 叢集。 Azure Functions 提供便利的方式來建立公用端點，以便透過虛擬網路閘道將資料推送到 Kafka on HDInsight。

> [!NOTE]
> 本文件的重點在於讓 Azure Functions 能與 Kafka on HDInsight 通訊所需的步驟。 範例本身只是基本 Kafka 生產者，用以證明此組態能運作。

## <a name="prerequisites"></a>先決條件

* Azure 函式應用程式。 如需詳細資訊，請參閱[建議您的第一個函式](../../azure-functions/functions-create-first-azure-function.md)文件。

* Azure 虛擬網路。 若要使用 Azure Functions，虛擬網路必須使用下列其中一個 IP 範圍：

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    如需詳細資訊，請參閱[將應用程式與 Azure 虛擬網路整合](../../app-service/web-sites-integrate-with-vnet.md)文件。

* Kafka on HDInsight 叢集。 如需有關建立 Kafka on HDInsight 叢集的詳細資訊，請參閱[建立 Kafka 叢集](apache-kafka-get-started.md)文件。

    > [!IMPORTANT]
    > 在叢集設定期間，您必須使用__進階設定__步驟來選取 HDInsight 所使用的 Azure 虛擬網路與子網路。 選取在前一個步驟建立的虛擬網路和子網路。

    如需 Kafka 和虛擬網路的詳細資訊，請參閱[透過虛擬網路連線至 Kafka](apache-kafka-connect-vpn-gateway.md) 文件。

## <a name="architecture"></a>架構

Kafka on HDInsight 包含在 Azure 虛擬網路中。 Azure Functions 可以使用點對站閘道來與虛擬網路進行通訊。 下圖是此網路拓撲的圖表：

![連線到 HDInsight 的 Azure Functions 架構](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>設定 Kafka

本節中的資訊會備妥 Kafka 叢集，以接受來自 Azure 函式的資料。 它涵蓋下列設定動作：

* IP 通告
* 蒐集 Kafka 訊息代理程式 IP 位址
* 建立 Kafka 主題

### <a name="configure-kafka-for-ip-advertising"></a>設定 Kafka 進行 IP 公告

Zookeeper 預設會將 Kafka 代理程式的網域名稱傳回給用戶端。 若沒有 DNS 伺服器，此組態無法運作，因為用戶端 (Azure Functions) 無法解析虛擬網路的名稱。 針對此設定，使用下列步驟來設定 Kafka 以公告 IP 位址而不是網域名稱：

1. 使用網頁瀏覽器，移至 https://CLUSTERNAME.azurehdinsight.net。 將 __CLUSTERNAME__ 取代為 HDInsight 叢集上 Kafka 的名稱。

    出現提示時，請使用叢集的 HTTPS 使用者名稱和密碼。 此時會顯示叢集的 Ambari Web UI。

2. 若要檢視 Kafka 上的資訊，請從左邊的清單選取 [Kafka]。

    ![反白顯示 Kafka 的服務清單](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. 若要檢視 Kafka 組態，請從正上方選取 [Configs (設定)]。

    ![Kafka 的 Configs (設定) 連結](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. 若要找出 __kafka-env__ 組態，請在右上角的 [Filter (篩選)] 欄位中輸入 `kafka-env`。

    ![Kafka 組態，找出 kafka-env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

5. 若要設定 Kafka 公告 IP 位址，請在 [kafka-env-template] 欄位的底部加入下列文字︰

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. 若要設定 Kafka 接聽的介面，請在右上角的 [Filter (篩選)] 欄位中輸入 `listeners`。

7. 若要設定 Kafka 在所有網路介面上接聽，請將 [listeners (接聽程式)] 欄位的值變更為 `PLAINTEXT://0.0.0.0:9092`。

8. 若要儲存組態變更，請使用 [Save (儲存)] 按鈕。 輸入描述變更的文字訊息。 儲存變更後，請選取 [OK (確定)]。

    ![儲存組態按鈕](./media/apache-kafka-azure-functions/save-button.png)

9. 若要避免重新啟動 Kafka 時發生錯誤，請使用 [Service Actions (服務動作)] 按鈕，然後選取 [Turn On Maintenance Mode (開啟維護模式)]。 選取 [OK (確定)] 以完成此作業。

    ![服務動作，反白顯示開啟維護](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. 若要重新啟動 Kafka，請使用 [Restart (重新啟動)] 按鈕，然後選取 [Restart All Affected (重新啟動所有受影響項目)]。 確認重新啟動，然後在作業完成之後使用 [OK (確定)] 按鈕。

    ![重新啟動按鈕，反白顯示重新啟動所有受影響項目](./media/apache-kafka-azure-functions/restart-button.png)

11. 若要停用維護模式，請使用 [Service Actions (服務動作)] 按鈕，然後選取 [Turn Off Maintenance Mode (關閉維護模式)]。 選取 [OK (確定)] 以完成此作業。

### <a name="get-the-kafka-broker-ip-address"></a>取得 Kafka 訊息代理程式 IP 位址

使用下列其中一個方法來擷取 Kafka 叢集中節點的完整網域名稱 (FQDN) 與 IP 位址：

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

此命令假設 `<resourcegroupname>` 是包含虛擬網路的 Azure 資源群組名稱。

從傳回的名稱清單中，選取背景工作節點的 IP 位址。 此節點的內部完整網域名稱開頭為字母 `wn`。 函式會使用此 IP 位址來與 Kafka 通訊。

### <a name="create-a-kafka-topic"></a>建立 Kafka 主題

Kafka 會將資料儲存在__主題__中。 將資料從 Azure 函式傳送到 Kafka 之前，您必須建立函式。

若要建立主題，請使用[建立 Kafka 叢集](apache-kafka-get-started.md)文件中的步驟。

## <a name="create-a-function-that-sends-to-kafka"></a>建立可將資料傳送至 Kafka 的函式

> [!NOTE]
> 本節中的步驟會建立 JavaScript 函式，該函式會使用 [kafka-node](https://www.npmjs.com/package/kafka-node) 套件將資料發佈至 Kafka：

1. 建立新的 __WebHook + API__ 函式，然後選取 __JavaScript__ 作為語言。 如需建立新函式的詳細資訊，請參閱[建議您的第一個函式](../../azure-functions/functions-create-first-azure-function.md#create-function)文件。

2. 使用下列程式碼作為函式的主體：

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    將 `'test'` 取代為在 HDInsight 叢集上建立的 Kafka 主題名稱。

    將 `10.1.0.7` 取代為您稍早擷取的 IP 位址。 保留 `:9092` 值。 9092 是 Kafka 進行接聽的連接埠。

    使用 [儲存] 按鈕來儲存變更。

    ![具有儲存按鈕的編輯器](./media/apache-kafka-azure-functions/function-editor.png)

3. 從函式編輯器的右邊，選取 [檢視檔案]。 選取 [+ 新增] 並新增名為 `package.json` 的新檔案。 這個檔案用於指定 `kafka-node` 套件的相依性。

    ![新增檔案](./media/apache-kafka-azure-functions/add-files.png)

4. 若要編輯新檔案，請從 [檢視檔案] 清單中選取 `package.json`。 使用下列文字做為檔案的內容：

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    使用 [儲存] 按鈕來儲存變更。

5. 若要安裝 `kafka-node` 套件，請使用 [Azure Functions JavaScript 開發人員指南](../../azure-functions/functions-reference-node.md#node-version-and-package-management)的「節點版本和套件管理」一節。

    > [!NOTE]
    > 安裝 kafka-node 套件時，您可能會收到幾個錯誤。 您可以放心地忽略這些錯誤。

## <a name="run-the-function"></a>執行函式

從函式編輯器的右邊，選取 [測試]。 保留測試的預設設定，然後選取 [執行]。 執行測試時，它會將 `name` 參數傳遞給函式。 這個參數包含 `Azure` 值，此值是由函式插入 Kafka 中。

![測試對話方塊螢幕擷取畫面](./media/apache-kafka-azure-functions/function-test-dialog.png)

若要在測試執行時，檢視函式所記錄的資訊，請選取頁面的底部 [記錄]。 再次執行測試以產生記錄資訊。

![函式記錄輸出的範例](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>確認資料位於 Kafka 中

若要確認資料已送達 Kafka 主題，請使用[建立 Kafka 叢集](apache-kafka-get-started.md#produce-and-consume-records)文件的「產生和取用記錄」一節中的資訊。 `kafka-console-consumer` 會讀取主題中的資料，並顯示主題中儲存的訊息清單。

## <a name="next-steps"></a>後續步驟

使用下列連結以了解如何使用 HDInsight 上的 Apache Kafka：

* [開始使用 HDInsight 上的 Kafka](apache-kafka-get-started.md)

* [使用 MirrorMaker 建立 Apache Kafka on HDInsight 複本](apache-kafka-mirroring.md)

* [使用 Apache Storm 搭配 HDInsight 上的 Kafka](../hdinsight-apache-storm-with-kafka.md)

* [使用 Apache Spark 搭配 Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [透過 Azure 虛擬網路連線到 Kafka](apache-kafka-connect-vpn-gateway.md)
