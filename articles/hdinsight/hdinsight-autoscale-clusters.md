---
title: 自動調整 Azure HDInsight 叢集（預覽）
description: 使用 Azure HDInsight 自動調整功能自動執行 Apache Hadoop 擴充叢集
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 9071b41ab39c62f639b62a439e4d2530a7d7e11b
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880048"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>自動調整 Azure HDInsight 叢集（預覽）

> [!Important]
> 自動調整功能僅適用于在5月 8 2019 之後建立的 Spark、Hive 和 MapReduce 叢集。 

Azure HDInsight 的叢集自動調整功能會自動相應增加和減少叢集中的背景工作節點數目。 目前無法調整叢集中的其他節點類型。  在建立新的 HDInsight 叢集時，可以設定最小和最大的背景工作節點數目。 自動調整會監視分析負載的資源需求，並相應增加或減少背景工作角色節點的數目。 這項功能不需額外付費。

## <a name="cluster-compatibility"></a>叢集相容性

下表描述與自動調整功能相容的叢集類型和版本。

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| 不含 ESP 的 HDInsight 3。6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 不含 ESP 的 HDInsight 4。0 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 使用 ESP 的 HDInsight 3。6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 使用 ESP 的 HDInsight 4。0 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |

## <a name="how-it-works"></a>運作方式

您可以為您的 HDInsight 叢集選擇以負載為基礎的調整或以排程為基礎的調整。 以負載為基礎的調整會在您設定的範圍內變更叢集中的節點數目，以確保最佳的 CPU 使用率，並將執行成本降至最低。

以排程為基礎的調整會根據在特定時間生效的條件來變更叢集中的節點數目。 這些條件會將叢集調整為所需的節點數目。

### <a name="metrics-monitoring"></a>計量監視

自動調整會持續監視叢集，並收集下列計量：

* **擱置中的 CPU 總計**：開始執行所有擱置中容器時所需的核心總數。
* **擱置中的記憶體總計**：開始執行所有擱置中容器時所需的記憶體總計 (MB)。
* **可用的 CPU 總計**：作用中背景工作節點上所有未使用核心的總和。
* **可用的記憶體總計**：作用中背景工作節點上所有未使用記憶體的總和 (MB)。
* **每個節點的已使用記憶體**：背景工作節點上的負載。 已使用 10 GB 記憶體的背景工作節點會被視為所承受的負載高於已使用 2 GB 記憶體的背景工作節點。
* **每個節點的應用程式主機數目**：背景工作節點上執行的應用程式主機 (AM) 容器數目。 裝載兩個 AM 容器的背景工作節點會被視為比裝載 zero AM 容器的背景工作節點更重要。

系統每隔 60 秒就會檢查上述計量。 自動調整會根據這些計量來進行相應增加和相應減少決策。

### <a name="load-based-cluster-scale-up"></a>以負載為基礎的叢集相應增加

當偵測到下列情況時，自動調整將會發出相應增加要求：

* 擱置中的 CPU 總計大於可用的 CPU 總計超過3分鐘。
* 暫止的記憶體總計大於可用的記憶體總計超過3分鐘。

HDInsight 服務會計算需要多少個新的背景工作節點，以符合目前的 CPU 和記憶體需求，然後發出相應增加要求來新增所需的節點數目。

### <a name="load-based-cluster-scale-down"></a>以負載為基礎的叢集相應減少

當偵測到下列情況時，自動調整將會發出相應減少要求：

* 擱置中的 CPU 總計小於可用的 CPU 總計超過 10 分鐘。
* 擱置中的記憶體總計小於可用的記憶體總計超過 10 分鐘。

根據每個節點的 AM 容器數目，以及目前的 CPU 和記憶體需求，自動調整會發出移除特定節點數目的要求。 服務也會根據目前的作業執行，偵測哪些節點是候選項目。 相應減少作業會先會 add-on 節點，然後將它們從叢集中移除。

## <a name="get-started"></a>開始使用

### <a name="create-a-cluster-with-load-based-autoscaling"></a>建立具有以負載為基礎之自動調整的叢集

若要啟用以負載為基礎的自動調整功能，請在一般叢集建立程式中完成下列步驟：

1. 選取 [自訂 (大小、設定、應用程式)]，而非 [快速建立]。
1. 在 [**自訂**步驟5（叢集大小）] 上，勾選 [背景**工作節點自動** **調整**] 核取方塊。
1. 選取 [**自動調整類型**] 下的 [以**載入為基礎**] 選項。
1. 針對下列屬性輸入所需的值：  

    * 初始**背景工作節點數目**。  
    * 背景工作節點**最小**數目。  
    * 背景工作節點**最大**數目。  

    ![啟用背景工作節點以負載為基礎的自動調整](./media/hdinsight-autoscale-clusters/hdinsight-using-autoscale.png)

背景工作節點的初始數目必須介於最小值和最大值 (含) 之間。 此值會定義叢集建立時的初始大小。 背景工作節點的最小數目必須大於零。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>建立具有以排程為基礎之自動調整的叢集

若要啟用以排程為基礎的自動調整功能，請在一般叢集建立程式中完成下列步驟：

1. 選取 [自訂 (大小、設定、應用程式)]，而非 [快速建立]。
1. 在 [**自訂**步驟5（叢集大小）] 上，勾選 [背景**工作節點自動** **調整**] 核取方塊。
1. 輸入背景**工作節點的數目**，以控制相應增加叢集的限制。
1. 選取 [**自動調整類型**] 下的 [**排程依據**] 選項。
1. 按一下 [**設定**] 以開啟 [**自動**調整規模設定] 視窗。
1. 選取您的時區，然後按一下 [ **+ 新增條件**]
1. 選取應套用新條件的周間日。
1. 編輯條件應生效的時間，以及應調整叢集的節點數目。
1. 視需要新增更多條件。

    ![啟用背景工作節點以排程為基礎的建立](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

節點數目必須介於1和您在新增條件之前輸入的背景工作節點數目之間。

### <a name="final-creation-steps"></a>最後的建立步驟

針對負載型和以排程為基礎的調整，請按一下 [背景**工作節點大小**] 和 [前端**節點大小**] 來選取背景工作節點的 VM 類型。 為每個節點類型選擇 VM 類型之後，您可以看到整個叢集的預估成本範圍。 調整 VM 類型以符合您的預算。

![啟用背景工作節點以排程為基礎的自動調整節點大小](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

您的訂用帳戶擁有每個區域的容量配額。 您前端節點的核心總數與背景工作節點的最大數目結合之後，不能超過容量配額。 但是這個配額是軟性限制；您一律可以建立支援票證，輕鬆增加配額。

> [!Note]  
> 如果您超過總核心配額限制，將會收到錯誤訊息，指出「最大節點超過此區域中的可用核心數，請選擇另一個區域，或聯絡支援人員增加配額」。

如需使用 Azure 入口網站建立 HDInsight 叢集的詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立叢集

#### <a name="load-based-autoscaling"></a>以負載為基礎的自動調整

`autoscale`您可以透過將節點加入至具有屬性 >  `minInstanceCount`和`computeProfile` `workernode` `maxInstanceCount`的區段，以使用載入型自動調整 Azure Resource Manager 範本來建立 HDInsight 叢集如下列 json 程式碼片段所示。

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

如需使用 Resource Manager 範本建立叢集的詳細資訊，請參閱[使用 Resource Manager 範本在 HDInsight 中建立 Apache Hadoop 叢集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。  

#### <a name="schedule-based-autoscaling"></a>以排程為基礎的自動調整

您可以`autoscale`藉由將節點新增`computeProfile`  >  `workernode`至區段，以排程為基礎的自動調整 Azure Resource Manager 範本來建立 HDInsight 叢集。 `autoscale` 節點`recurrence`包含具有`timezone`和的`schedule` ，可描述何時會進行變更。

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>針對正在執行的叢集啟用和停用自動調整

#### <a name="using-the-azure-portal"></a>使用 Azure 入口網站
若要在執行中的叢集上啟用自動調整，請選取 **設定** 底下的 叢集**大小** 然後按一下 [**啟用自動**調整]。 選取您想要的自動調整類型，並輸入以負載為基礎或以排程為基礎的縮放選項。 最後，按一下 [ **儲存**]。

![啟用以背景工作節點排程為基礎的自動調整執行叢集](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>使用 REST API
若要使用 REST API 在執行中的叢集上啟用或停用自動調整，請對自動調整端點發出 POST 要求，如下列程式碼片段所示：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

在要求承載中使用適當的參數。 下列 json 承載可用來啟用自動調整。 使用承載來停用自動調整。 `{autoscale: null}`

```json
{ autoscale: { capacity: { minInstanceCount: 1, maxInstanceCount: 2 } } }
```

請參閱上一節關於[啟用以負載為基礎的自動](#load-based-autoscaling)調整，以取得所有裝載參數的完整描述。

## <a name="best-practices"></a>最佳做法

### <a name="choosing-load-based-or-schedule-based-scaling"></a>選擇以負載為基礎或以排程為基礎的調整

在決定要選擇哪一種模式之前，請先考慮下列因素：

* 負載變異數：叢集的負載會在特定時間以一致的模式遵循指定的日期。 如果沒有，以負載為基礎的排程是較好的選擇。
* SLA 需求：自動調整規模是被動的，而不是預測。 負載開始增加的時間和叢集必須處於其目標大小之間是否有足夠的延遲？ 如果有嚴格的 SLA 需求，而負載是固定的已知模式，則 [以排程為基礎] 是較好的選擇。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考慮相應增加或相應減少作業的延遲

調整作業可能需要10到20分鐘的時間才能完成。 設定自訂排程時，請為此延遲進行規劃。 例如，如果您需要在上午9:00 的叢集大小為20，請將排程觸發程式設定為較早的時間，例如 8:30 AM，讓調整作業已完成 9:00 AM。

### <a name="preparation-for-scaling-down"></a>向下調整的準備

在叢集相應減少程式期間，自動調整會將節點解除委任以符合目標大小。 如果這些節點上有執行中的工作，自動調整會等到工作完成為止。 由於每個背景工作節點也會在 HDFS 中提供角色，因此暫存資料將會移至其餘的節點。 因此，您應該確定其餘節點上有足夠的空間來裝載所有的暫存資料。 

執行中的作業將會繼續執行並完成。 暫止的工作將會等候排程為正常，且有較少的可用背景工作節點。

## <a name="monitoring"></a>監視

### <a name="cluster-status"></a>叢集狀態

Azure 入口網站中所列的叢集狀態可協助您監視自動調整活動。

![啟用背景工作節點負載型自動調整叢集狀態](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

下列清單會說明您可能會看到的所有叢集狀態訊息。

| 叢集狀態 | 說明 |
|---|---|
| Running | 叢集正常運作。 所有先前的自動調整活動都已順利完成。 |
| 正在更新  | 正在更新叢集自動調整設定。  |
| HDInsight 設定  | 叢集擴充或相應減少作業進行中。  |
| 更新錯誤  | HDInsight 在自動調整設定更新期間發生問題。 客戶可以選擇重試更新或停用自動調整。  |
| Error  | 叢集發生問題，因此無法使用。 刪除此叢集並建立一個新叢集。  |

若要查看您叢集中目前的節點數目，請移至叢集 [**總覽**] 頁面上的 [叢集**大小**] 圖表，或按一下 [**設定**] 底下的 [叢集**大小**]。

### <a name="operation-history"></a>作業歷程記錄

您可以將叢集相應增加和相應減少歷程記錄視為叢集計量的一部分來查看。 您也可以列出過去一天、一周或其他一段時間的所有調整動作。

選取 [**監視**] 下的 [**計量**]。 然後**按一下 [計量**] 下拉式清單方塊中的 [**新增**計量和作用中背景**工作的數目**]。 按一下右上方的按鈕以變更時間範圍。

![啟用以背景工作節點排程為基礎的自動調整度量](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>後續步驟

* 閱讀[調整規模最佳做法](hdinsight-scaling-best-practices.md)，了解手動調整叢集規模的最佳做法
