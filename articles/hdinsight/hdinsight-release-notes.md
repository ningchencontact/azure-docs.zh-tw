---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 5d9ca8d0df3eb0186add5c40765c87a4409a5660
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926416"
---
# <a name="release-notes"></a>版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>總結

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼 Apache Hadoop 與 Apache Spark 分析使用。

## <a name="release-date-11072019"></a>發行日期：11/07/2019

此版本適用于 HDInsight 3.6 和4.0。

> [!IMPORTANT]  
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [HDInsight 版本控制文件](hdinsight-component-versioning.md)。


## <a name="new-features"></a>新功能

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight 身分識別代理程式（HIB 表示）（預覽）

HDInsight 身分識別代理程式（HIB 表示）可讓使用者使用多重要素驗證（MFA）登入 Apache Ambari，並取得所需的 Kerberos 票證，而不需要 Azure Active Directory Domain Services （AAD-DS）中的密碼雜湊。 目前 HIB 表示僅適用于透過 ARM 範本部署的叢集。

### <a name="kafka-rest-api-proxy-preview"></a>Kafka Rest API Proxy （預覽）

Kafka Rest API Proxy 可透過安全的 AAD 授權和 OAuth 通訊協定，以單鍵方式部署具有 Kafka 叢集的高可用性 REST Proxy。 

### <a name="auto-scale"></a>自動調整規模

Azure HDInsight 的自動調整現已在 Apache Spark 和 Hadoop 叢集類型的所有區域中正式推出。 這項功能可讓您以更符合成本效益且更具生產力的方式來管理海量資料分析工作負載。 現在您可以將 HDInsight 叢集的使用優化，並只針對您需要的部分付費。

視您的需求而定，您可以在負載式自動調整與排程式自動調整之間進行選擇。 以負載為基礎的自動調整可以根據目前的資源需求相應增加和減少叢集大小，而以排程為依據的自動調整可以根據預先定義的排程來變更叢集大小。 

HBase 和 LLAP 工作負載的自動調整支援也是公開預覽。 如需詳細資訊，請參閱[自動調整 Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters)叢集。

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>適用于 Apache HBase 的 HDInsight 加速寫入 

加速寫入會使用 Azure 高階 SSD 受控磁碟來改善 Apache HBase 預寫記錄檔 (WAL) 的效能。 如需詳細資訊，請參閱[適用於 Apache HBase 的 Azure HDInsight 加速寫入](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes)。

### <a name="custom-ambari-db"></a>自訂 Ambari DB

HDInsight 現在提供新的容量，讓客戶能夠使用自己的 SQL DB 進行 Ambari。 客戶現在可以選擇適當的 SQL DB 進行 Ambari，並根據自己的業務成長需求輕鬆地升級。 部署是使用 Azure Resource Manager 範本來完成。 如需詳細資訊，請參閱[使用自訂 AMBARI DB 設定 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db)叢集。

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>F 系列虛擬機器現已可供 HDInsight 使用

F 系列虛擬機器（Vm）是開始使用具有輕處理需求的 HDInsight 的絕佳選擇。 F 系列的每小時訂價較低，在 Azure 產品組合中，就每一 vCPU 的「Azure 計算單位」(ACU) 而言，具有最佳的價格/性能比表現。 如需詳細資訊，請參閱[為您的 Azure HDInsight 叢集選取正確的 VM 大小](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size)。

## <a name="deprecation"></a>淘汰

### <a name="g-series-virtual-machine-deprecation"></a>G 系列虛擬機器淘汰
在此版本中，不會再于 HDInsight 中提供 G 系列 Vm。

### <a name="dv1-virtual-machine-deprecation"></a>Dv1 虛擬機器淘汰
在此版本中，將 Dv1 Vm 與 HDInsight 搭配使用已被取代。 Dv1 的任何客戶要求都會自動提供 Dv2。 Dv1 與 Dv2 Vm 之間沒有任何價格差異。

## <a name="behavior-changes"></a>行為變更

### <a name="cluster-managed-disk-size-change"></a>叢集受控磁片大小變更
HDInsight 會提供叢集的受控磁碟空間。 在此版本中，新建立之叢集中每個節點的受控磁片大小都會變更為 128 GB。

## <a name="upcoming-changes"></a>即將推出的變更
即將發行的版本中將會發生下列變更。 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>移至 Azure 虛擬機器擴展集
HDInsight 現在會使用 Azure 虛擬機器來布建叢集。 從12月開始，HDInsight 會改為使用 Azure 虛擬機器擴展集。 深入瞭解[Azure 虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。

### <a name="hbase-20-to-21"></a>HBase 2.0 至2。1
在即將推出的 HDInsight 4.0 版本中，HBase 版本將會從2.0 版升級到2.1。

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>ESP 叢集的 A 系列虛擬機器淘汰
A 系列 Vm 可能會因為 CPU 和記憶體容量相對較低而造成 ESP 叢集問題。 在即將發行的版本中，A 系列 Vm 將會被取代，以建立新的 ESP 叢集。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會繼續改善叢集的可靠性和效能。 

## <a name="component-version-change"></a>元件版本變更
此版本沒有元件版本變更。 您可以在[這裡](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)找到 hdinsight 4.0 和 hdinsight 3.6 的目前元件版本。
