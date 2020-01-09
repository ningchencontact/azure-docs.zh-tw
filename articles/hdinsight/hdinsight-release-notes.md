---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435444"
---
# <a name="release-notes"></a>版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼 Apache Hadoop 與 Apache Spark 分析使用。

## <a name="release-date-12172019"></a>發行日期：12/17/2019

此版本適用于 HDInsight 3.6 和4.0。

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 版本控制文件](hdinsight-component-versioning.md)。


## <a name="new-features"></a>新功能

### <a name="service-tags"></a>服務標籤
服務標籤可讓您輕鬆地限制對 Azure 服務的網路存取，藉此簡化 Azure 虛擬機器和 Azure 虛擬網路的安全性。 您可以使用網路安全性群組（NSG）規則中的服務標籤，以允許或拒絕全域或每個 Azure 區域的特定 Azure 服務流量。 Azure 會提供每個標籤基礎之 IP 位址的維護。 適用于網路安全性群組（Nsg）的 HDInsight 服務標記是健全狀況和管理服務的 IP 位址群組。 這些群組有助於降低建立安全性規則的複雜性。 HDInsight 客戶可以透過 Azure 入口網站、PowerShell 和 REST API 來啟用服務標記。 如需詳細資訊，請參閱[Azure HDInsight 的網路安全性群組（NSG）服務標記](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags)。

### <a name="custom-ambari-db"></a>自訂 Ambari DB
HDInsight 現在可讓您使用自己的 SQL DB 進行 Apache Ambari。 您可以從 Azure 入口網站或透過 resource manager 範本設定此自訂 Ambari DB。  這項功能可讓您選擇適合您處理和容量需求的 SQL DB。 您也可以輕鬆地升級，以符合業務成長需求。 如需詳細資訊，請參閱[使用自訂 AMBARI DB 設定 HDInsight](hdinsight-custom-ambari-db.md)叢集。

![自訂 Ambari DB](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>淘汰
此版本沒有任何棄用功能。 若要準備開始進行棄用功能，請參閱即將進行的[變更](#upcoming-changes)。

## <a name="behavior-changes"></a>行為變更
此版本沒有任何行為變更。 若要準備好進行近期的行為變更，請參閱即將進行的[變更](#upcoming-changes)。

## <a name="upcoming-changes"></a>即將推出的變更
即將發行的版本中將會發生下列變更。 

### <a name="transport-layer-security-tls-12-enforcement"></a>傳輸層安全性（TLS）1.2 強制
傳輸層安全性 (TLS) 和安全通訊端層 (SSL) 是密碼編譯通訊協定，可透過電腦網路提供通訊安全性。 如需詳細資訊，請參閱[傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)。 雖然 Azure HDInsight 叢集接受公用 HTTPS 端點上的 TLS 1.2 連線，但 TLS 1.1 仍然支援舊版用戶端的回溯相容性。

從下一版開始，您將能夠加入宣告並設定新的 HDInsight 叢集，只接受 TLS 1.2 連線。 

從6/30/2020 開始，Azure HDInsight 將會強制執行所有 HTTPS 連線的 TLS 1.2 或更新版本。 我們建議您確定您的所有用戶端都已準備好處理 TLS 1.2 或更新版本。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移至 Azure 虛擬機器擴展集
HDInsight 現在會使用 Azure 虛擬機器來布建叢集。 從2006年 2 2020 開始（將于稍後傳達確切的日期），HDInsight 會改為使用 Azure 虛擬機器擴展集。 深入瞭解[Azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark 叢集節點大小變更 
在即將推出的版本中：
- ESP Spark 叢集所允許的最小節點大小會變更為 Standard_D13_V2。 
- A 系列 Vm 將會淘汰以建立新的 ESP 叢集，因為 A 系列 Vm 可能會因為 CPU 和記憶體容量相對較低而造成 ESP 叢集問題。

### <a name="hbase-20-to-21"></a>HBase 2.0 至2。1
在即將推出的 HDInsight 4.0 版本中，HBase 版本將會從2.0 版升級到2.1。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會繼續改善叢集的可靠性和效能。 

## <a name="component-version-change"></a>元件版本變更
我們已將 HDInsight 3.6 支援擴充至2020年12月31日。 您可以在[支援的 HDInsight 版本](hdinsight-component-versioning.md#supported-hdinsight-versions)中找到更多詳細資料。

HDInsight 4.0 沒有元件版本變更。

Apache Zeppelin on HDInsight 3.6： 0.7.0--> iisnode-inspector-0.7.3.dll。 

您可以在[此](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)檔中找到最新的元件版本。

## <a name="new-regions"></a>新區域

### <a name="uae-north"></a>阿拉伯聯合大公國北部
阿拉伯聯合大公國北部的管理 Ip 為： `65.52.252.96` 和 `65.52.252.97`。
