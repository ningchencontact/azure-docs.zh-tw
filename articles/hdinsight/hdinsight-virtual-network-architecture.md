---
title: Azure HDInsight 虛擬網路架構
description: 當您在 Azure 虛擬網路中建立 HDInsight 叢集時，請了解可用的資源。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 41420497bffd0abdc598e4c86b2dbda1466b2ce1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66252857"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 虛擬網路架構

本文說明當您部署至自訂的 Azure 虛擬網路的 HDInsight 叢集時所存在的資源。 這項資訊將協助您將內部部署資源連接到 Azure 中的 HDInsight 叢集。 如需有關 Azure 虛擬網路的詳細資訊，請參閱 <<c0> [ 什麼是 Azure 虛擬網路？](../virtual-network/virtual-networks-overview.md)。

## <a name="resource-types-in-azure-hdinsight-clusters"></a>在 Azure HDInsight 叢集中的資源類型

Azure HDInsight 叢集有不同類型的虛擬機器或節點。 每個節點類型系統的作業中扮演的角色。 下表摘要說明這些節點型別以及其在叢集中的角色。

| 類型 | 描述 |
| --- | --- |
| 前端節點 |  Apache Storm 以外的所有叢集類型，針對前端節點會裝載管理的分散式應用程式執行的處理程序。 在前端節點也是您可以透過 ssh 連線到節點，並執行所有叢集資源執行時，必須再都協調的應用程式。 前端節點的數目是在所有叢集類型的兩個固定的。 |
| ZooKeeper 節點 | Zookeeper 協調工作之間進行資料處理的節點。 它也會選出領導者的前端節點，並追蹤哪一個前端節點執行特定的主要服務。 ZooKeeper 節點的數目是在三個固定的。 |
| 背景工作角色節點 | 表示支援的資料處理功能的節點。 背景工作角色節點可以加入或移除叢集，以調整的運算能力和管理成本。 |
| R Server 邊緣節點 | R Server 邊緣節點代表您可以透過 ssh 連線到節點，並執行所有叢集資源執行時，必須再都協調的應用程式。 邊緣節點不會參與叢集內的資料分析。 此節點也會裝載 R Studio Server，讓您執行使用瀏覽器的 R 應用程式。 |
| 區域節點 | 對於 HBase 叢集類型中，（也稱為資料節點） 的區域節點會執行區域伺服器。 區域伺服器提供，並管理由 HBase 資料的一部分。 區域節點可以加入或移除叢集，以調整的運算能力和管理成本。|
| Nimbus 節點 | 對於 Storm 叢集類型中，Nimbus 節點會提供類似於前端節點的功能。 Nimbus 節點會將工作指派給透過協調執行的 Storm 拓撲的 Zookeeper 叢集中的其他節點。 |
| 監督員節點 | 對於 Storm 叢集類型中，監督員節點執行 Nimbus 節點，以執行所需的處理所提供的指示。 |

## <a name="basic-virtual-network-resources"></a>基本的虛擬網路資源

下圖顯示在 Azure 中的 HDInsight 節點和網路資源的位置。

![建立自訂的 Azure VNET 中的 HDInsight 實體的圖表](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

HDInsight 部署到 Azure 虛擬網路時存在的預設資源包含在上表中，以及支援的虛擬網路之間，以及外部網路通訊的網路裝置中所述的叢集節點類型。

下表摘要至自訂的 Azure 虛擬網路部署 HDInsight 時所建立的九個叢集節點。

| 資源類型 | 出現數目 | 詳細資料 |
| --- | --- | --- |
|前端節點 | two |    |
|Zookeeper 節點 | three | |
|背景工作角色節點 | two | 這個數目可以因叢集組態和調整。 適用於 Apache Kafka，都需要至少三個背景工作節點。  |
|閘道節點 | two | 閘道節點會在 Azure 上建立，但不會顯示您的訂用帳戶中的 Azure 虛擬機器。 如果您需要重新啟動這些節點，請連絡支援服務。 |

搭配 HDInsight 使用虛擬網路內，會自動建立的下列網路資源：

| 網路功能資源 | 出現數目 | 詳細資料 |
| --- | --- | --- |
|負載平衡器 | three | |
|網路介面 | 九個 | 這個值根據正常的叢集，其中每個節點都有它自己的網路介面。 九個介面是兩個前端節點、 三個 zookeeper 節點、 兩個背景工作節點和上表中所述的兩個閘道節點。 |
|公用 IP 位址 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>端點連接到 HDInsight

您可以存取您的 HDInsight 叢集以三種方式：

- 在虛擬網路外部的 HTTPS 端點`CLUSTERNAME.azurehdinsight.net`。
- 直接連接到前端節點的 SSH 端點`CLUSTERNAME-ssh.azurehdinsight.net`。
- 虛擬網路內的 HTTPS 端點`CLUSTERNAME-int.azurehdinsight.net`。 請注意 「-int 」 在此 URL。 此端點將會解析該虛擬網路的私人 IP，而且無法從公用網際網路存取。

下列三個端點每個會指派負載平衡器。

公用 IP 位址也會提供兩個端點，允許從虛擬網路外部的連線。

1. 一個公用 IP 指派給負載平衡器的完整的網域名稱 (FQDN)，以從網際網路連接到叢集時使用`CLUSTERNAME.azurehdinsight.net`。
1. 第二個公用 IP 位址使用於 SSH 唯一的網域名稱`CLUSTERNAME-ssh.azurehdinsight.net`。

## <a name="next-steps"></a>後續步驟

* [保護傳入的流量具有私用端點的虛擬網路中的 HDInsight 叢集](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
