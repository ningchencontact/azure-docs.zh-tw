---
title: 使用 Azure HDInsight 疑難排解叢集建立失敗
description: 瞭解如何針對 Azure HDInsight 的叢集建立問題進行疑難排解。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/06/2019
ms.openlocfilehash: c7092b2cbcef01ef71261b6f5498cde56a40c358
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857244"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>使用 Azure HDInsight 疑難排解叢集建立失敗

下列問題是叢集建立失敗的最常見根本原因:

- 許可權問題
- 資源原則限制
- 防火牆
- 資源鎖定
- 不支援的元件版本
- 儲存體帳戶名稱限制
- 服務中斷

## <a name="permissions-issues"></a>權限問題

如果您使用 Data Lake Storage Gen 2, 請確定指派給 HDInsight 叢集的使用者指派受控識別是在 **儲存體 Blob 資料參與者**角色或**儲存體 blob 資料擁有者角色**中。 如需完整的安裝指示, 請參閱[使用 Azure Data Lake Storage Gen2 搭配 Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account)叢集。

如果您使用 Data Lake Storage Gen 1, 請參閱[這裡](../hdinsight-hadoop-use-data-lake-store.md)的安裝和設定指示。 HBase 叢集不支援 Data Lake Storage Gen 1, 而且在 HDInsight 4.0 版中不支援。

如果使用 Azure 儲存體, 請確定在叢集建立期間, 儲存體帳戶名稱是有效的。

## <a name="resource-policy-restrictions"></a>資源原則限制

以訂用帳戶為基礎的 Azure 原則可以拒絕建立公用 IP 位址。 建立 HDInsight 叢集需要兩個公用 IP。  

一般來說, 下列原則可能會影響叢集建立:

* 原則會防止在訂用帳戶內建立 IP 位址 & 負載平衡器。
* 防止建立儲存體帳戶的原則。
* 防止刪除網路資源的原則 (IP 位址/Load 平衡器)。

## <a name="firewalls"></a>防火牆

您的虛擬網路或儲存體帳戶上的防火牆可以拒絕與 HDInsight 管理 IP 位址的通訊。

允許來自下表中 IP 位址的流量。

| 來源 IP 位址 | 目的地 | Direction |
|---|---|---|
| 168.61.49.99 | *: 443 | 傳入 |
| 23.99.5.239 | *: 443 | 傳入 |
| 168.61.48.131 | *: 443 | 傳入 |
| 138.91.141.162 | *: 443 | 傳入 |

此外, 也請新增叢集建立所在區域特定的 IP 位址。 如需每個 Azure 區域的地址清單, 請參閱[HDInsight 管理 IP 位址](../hdinsight-management-ip-addresses.md)。

如果您使用的是 express route 或您自己的自訂 DNS 伺服器, 請參閱[規劃虛擬網路, 以 Azure HDInsight 連接多個網路](../hdinsight-plan-virtual-network-deployment.md#multinet)。

## <a name="resources-locks"></a>資源鎖定  

請確定[您的虛擬網路和資源群組](../../azure-resource-manager/resource-group-lock-resources.md)沒有任何鎖定。  

## <a name="unsupported-component-versions"></a>不支援的元件版本

請確定您使用的是[支援的 Azure HDInsight 版本](../hdinsight-component-versioning.md), 以及解決方案中的任何[Apache Hadoop 元件](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)。  

## <a name="storage-account-name-restrictions"></a>儲存體帳戶名稱限制

儲存體帳戶名稱不可超過24個字元, 且不能包含特殊字元。 這些限制也適用於儲存體帳戶中的預設容器名稱。

## <a name="service-outages"></a>服務中斷

檢查[Azure 狀態](https://status.azure.com/status)是否有任何可能的中斷或服務問題。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 虛擬網路延伸 Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [搭配使用 Azure 儲存體與 Azure HDInsight 叢集](../hdinsight-hadoop-use-blob-storage.md)
* [使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他工具在 HDInsight 中設定叢集](../hdinsight-hadoop-provision-linux-clusters.md)
