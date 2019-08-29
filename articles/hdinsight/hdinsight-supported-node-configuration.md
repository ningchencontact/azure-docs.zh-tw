---
title: Azure HDInsight 支援的節點設定
description: 瞭解 HDInsight 叢集節點的最低和建議設定。
keywords: vm 大小, 叢集大小, 叢集設定
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076208"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Azure HDInsight 的預設和建議節點設定為何？

本文討論 Azure HDInsight 叢集的預設和建議節點設定。

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>叢集的預設和建議節點設定和虛擬機器大小

下表列出 HDInsight 叢集的預設和建議的虛擬機器 (VM) 大小。  當您建立 PowerShell 或 Azure CLI 腳本來部署 HDInsight 叢集時, 必須要有此資訊, 才能瞭解要使用的 VM 大小。 

如果您在叢集中需要超過32個背景工作節點, 請選取具有至少8個核心和 14 GB RAM 的前端節點大小。 

具有資料磁片的唯一叢集類型是 Kafka, 以及已啟用加速寫入功能的 HBase 叢集。 在這些情況下, HDInsight 支援 P30 和 S30 磁片大小。

如需每個 VM 類型規格的詳細資訊, 請參閱下列檔:

* [一般用途的虛擬機器大小:Dv2 系列1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [記憶體優化的虛擬機器大小:Dv2 系列11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [一般用途的虛擬機器大小:Av2 系列1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>除了巴西南部和日本西部以外的所有支援區域

> [!Note]
> 若要取得用於 powershell 和其他腳本的 SKU 識別碼, 請將`Standard_`新增至下表中所有 VM sku 的開頭。 例如, `D12_v2`會變成`Standard_D12_v2`。

| 叢集類型 | Hadoop | hbase | 互動式查詢 | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| 前端：預設 VM 大小 | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| 前端：建議的 VM 大小 | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| 背景工作：預設 VM 大小 | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4個 D12_v2, 每個訊息代理程式有2個 S30 磁片 |
| 背景工作：建議的 VM 大小 | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| ZooKeeper：預設 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper：建議的 VM 大小 |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| ML 服務: 預設 VM 大小 |  |  |  |  |  | D4_v2 |  |
| ML 服務: 建議的 VM 大小 |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>僅限巴西南部和日本西部

| 叢集類型 | Hadoop | hbase | 互動式查詢 | Storm | Spark | ML 服務 |
|---|---|---|---|---|---|---|
| 前端：預設 VM 大小 | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| 前端：建議的 VM 大小 | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| 背景工作：預設 VM 大小 | D4 | D4 | D14 | D3 | D13 | D4 |
| 背景工作：建議的 VM 大小 | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| ZooKeeper：預設 VM 大小 |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper：建議的 VM 大小 |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| ML 服務: 預設 VM 大小 |  |  |  |  |  | D4 |
| ML 服務: 建議的 VM 大小 |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - 前端稱為 Storm 叢集類型的 Nimbus。
> - 背景工作角色稱為 Storm 叢集類型的「監督員」。
> - 背景工作角色稱為 HBase 叢集類型的「區域」。

## <a name="next-steps"></a>後續步驟

* [可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？](hdinsight-component-versioning.md)
