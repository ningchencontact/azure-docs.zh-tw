---
title: 自動調整 Azure HDInsight 叢集 （預覽）
description: 使用 HDInsight 自動調整功能自動調整叢集規模
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 6ec981164de0ff61b0e83d54255d046a1418ed96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000094"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>自動調整 Azure HDInsight 叢集 （預覽）

> [!Important]
> 自動調整功能僅適用於 Spark、 Hive 和 MapReduce 2019 8 日之後建立的叢集。 

Azure HDInsight 叢集中自動調整功能會自動調整背景工作節點數目在叢集中增加和減少。 無法在目前調整其他類型的叢集中的節點。  在建立新的 HDInsight 叢集時，可以設定最小和最大的背景工作節點數目。 然後，自動調整會監視 analytics 負載的資源需求，並相應增加或減少背景工作節點數目。 沒有這項功能需額外付費。

## <a name="cluster-compatibility"></a>叢集的相容性

下表說明叢集類型和版本相容的自動調整功能。

| Version | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 沒有 ESP | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 沒有 ESP HDInsight 4.0 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 使用 ESP HDInsight 3.6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 使用 ESP HDInsight 3.6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |

## <a name="how-it-works"></a>運作方式

您可以選擇負載為基礎的 scaler 或排程為基礎調整您的 HDInsight 叢集。 負載為基礎的 scaler 變更在您的叢集，請在您設定，以確保最佳的 CPU 使用率，並執行成本降到最低的範圍內的節點數目。

排程為基礎的調整變更叢集中的節點數目根據生效的特定時間的條件。 這些條件調整叢集節點所需的數目。

### <a name="metrics-monitoring"></a>計量監視

自動調整會持續監視叢集，並收集下列計量：

* **擱置中的 CPU 總計**：開始執行所有擱置中容器時所需的核心總數。
* **擱置中的記憶體總計**：開始執行所有擱置中容器時所需的記憶體總計 (MB)。
* **可用的 CPU 總計**：作用中背景工作節點上所有未使用核心的總和。
* **可用的記憶體總計**：作用中背景工作節點上所有未使用記憶體的總和 (MB)。
* **每個節點的已使用記憶體**：背景工作節點上的負載。 已使用 10 GB 記憶體的背景工作節點會被視為所承受的負載高於已使用 2 GB 記憶體的背景工作節點。
* **每個節點的應用程式主機數目**：背景工作節點上執行的應用程式主機 (AM) 容器數目。 裝載兩個的上午容器在背景工作節點會被視為比裝載零上午容器在背景工作節點更重要的。

系統每隔 60 秒就會檢查上述計量。 自動調整規模會相應增加和相應減少的決策，根據這些計量。

### <a name="load-based-cluster-scale-up"></a>負載為基礎的叢集相應增加

當偵測到下列條件時，自動調整規模就會發出相應增加要求：

* 暫止 CPU 總計大於可用的 CPU 總計超過 3 分鐘。
* 暫止的記憶體總計大於總可用記憶體超過 3 分鐘。

HDInsight 服務會計算以符合目前的 CPU 和記憶體需求，需要多少新的背景工作節點，並接著會發出新增所需的節點數目相應增加要求。

### <a name="load-based-cluster-scale-down"></a>負載為基礎的叢集相應減少

當偵測到下列條件時，自動調整規模就會發出相應減少要求：

* 擱置中的 CPU 總計小於可用的 CPU 總計超過 10 分鐘。
* 擱置中的記憶體總計小於可用的記憶體總計超過 10 分鐘。

根據每個節點和目前的 CPU 和記憶體需求的上午容器的數目，自動調整規模發出的要求，若要移除的節點數目。 此服務也會偵測節點是根據目前的工作執行的移除的候選項目。 相應減少作業先解除節點，並再將它們從叢集移除。

## <a name="get-started"></a>開始使用

### <a name="create-a-cluster-with-load-based-autoscaling"></a>建立叢集，並以負載為基礎的自動調整規模

若要啟用以負載為基礎的 scaler 的自動調整功能，請在一般叢集建立程序的一部分完成下列步驟：

1. 選取 [自訂 (大小、設定、應用程式)]  ，而非 [快速建立]  。
1. 在 **自訂**步驟 5 (**叢集大小**)，檢查**背景工作節點自動調整規模**核取方塊。
1. 選取的選項**負載為基礎**下方**自動調整類型**。
1. 下列屬性輸入所需的值：  

    * 初始**背景工作節點數目**。  
    * 背景工作節點**最小**數目。  
    * 背景工作節點**最大**數目。  

    ![啟用背景工作節點負載為基礎的自動調整選項](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

背景工作節點的初始數目必須介於最小值和最大值 (含) 之間。 此值會定義叢集建立時的初始大小。 背景工作節點的最小數目必須大於零。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>建立叢集與排程為基礎的自動調整

若要啟用以排程為基礎的 scaler 的自動調整功能，請在一般叢集建立程序的一部分完成下列步驟：

1. 選取 [自訂 (大小、設定、應用程式)]  ，而非 [快速建立]  。
1. 在 **自訂**步驟 5 (**叢集大小**)，檢查**背景工作節點自動調整規模**核取方塊。
1. 請輸入**數字的背景工作角色節點**，這會控制相應增加叢集的限制。
1. 選取的選項**排程為基礎**下方**自動調整類型**。
1. 按一下 **設定**來開啟**自動調整規模設定**視窗。
1. 選取您所在時區，然後按一下 **+ 新增條件**
1. 選取新的條件應套用至一周天數。
1. 編輯條件應該生效且應該調整叢集中的節點數目所花費的時間。
1. 如有需要請加入更多條件。

    ![啟用背景工作節點排程為基礎的自動調整選項](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

節點的數目必須介於 1 到您輸入之前新增條件的背景工作節點數目。

### <a name="final-creation-steps"></a>最終的建立步驟

以負載為基礎和以排程為基礎的調整，選取 依序按一下 背景工作角色節點的 VM 類型**背景工作角色節點大小**並**前端節點大小**。 選擇每個節點類型的 VM 類型之後，您可以看到整個叢集的預估的成本範圍。 調整以符合您預算的 VM 類型。

![啟用背景工作節點排程為基礎的自動調整選項](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

您的訂用帳戶擁有每個區域的容量配額。 您前端節點的核心總數與背景工作節點的最大數目結合之後，不能超過容量配額。 但是這個配額是軟性限制；您一律可以建立支援票證，輕鬆增加配額。

> [!Note]  
> 如果您超過的總核心配額限制時，您會收到下列錯誤訊息: '的最大的節點超過可用的核心，在此區域中，請選擇另一個區域或連絡支援人員增加配額。'

如需使用 Azure 入口網站建立 HDInsight 叢集的詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立叢集

#### <a name="load-based-autoscaling"></a>以負載為基礎的自動調整

您可以建立的 HDInsight 叢集與負載為基礎的自動調整的 Azure Resource Manager 範本，請藉由新增`autoscale`節點，以`computeProfile`  >  `workernode`屬性的區段`minInstanceCount`和`maxInstanceCount`為下列 json 程式碼片段所示。

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

#### <a name="schedule-based-autoscaling"></a>排程型自動調整

您可以建立的 HDInsight 叢集與排程為基礎的自動調整的 Azure Resource Manager 範本，請藉由新增`autoscale`節點，以`computeProfile`  >  `workernode`一節。 `autoscale`節點包含`recurrence`具有`timezone`和`schedule`描述時，變更才會發生。

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

若要啟用自動調整執行中叢集上，選取**叢集大小**下方**設定**。 然後按一下**啟用自動調整規模**。 選取您想要並輸入負載或排程為基礎的調整選項的自動調整的類型。 最後，按一下 [ **儲存**]。

![啟用背景工作節點排程為基礎的自動調整選項](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="best-practices"></a>最佳作法

### <a name="choosing-load-based-or-schedule-based-scaling"></a>選擇負載或排程調整規模

進行做出選擇哪一種模式的決策之前，先考慮下列因素：

* 載入的變異數： 未在叢集的負載遵循一致的模式在特定時間，在特定幾天。 如果沒有，以負載為基礎的排程是較好的選擇。
* SLA 需求：自動調整規模調整為被動，而不是 預測性。 將會有足夠的延遲時間時增加，當叢集必須位於目標大小會開始載入嗎？ 如果有嚴格的 SLA 需求，將負載是固定的已知的模式為基礎的排程' 是較好的選擇。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考慮擴展的延遲增加或相應減少作業

可能需要 10 到 20 分鐘才能完成調整作業。 設定自訂排程，在規劃此延遲。 比方說，如果您需要叢集大小為 20 日上午 9:00，設定排程觸發程序為較早的時間，例如上午 8:30，因此在調整作業已完成的上午 9:00。

### <a name="preparation-for-scaling-down"></a>準備進行相應減少

在叢集相應減少程序期間自動調整規模會將節點解除委任符合目標大小。 如果在這些節點上正在執行工作，自動調整規模會等到工作完成。 因為每個背景工作節點也會處理在 HDFS 中的角色，暫存資料會被移到剩餘的節點。 因此您應該確定主控所有暫存資料的其餘節點上沒有足夠的空間。 

執行中的作業會繼續執行並完成。 擱置中的工作將會等候排定為正常大小並且較少的可用的背景工作節點。

## <a name="monitoring"></a>監視

### <a name="cluster-status"></a>叢集狀態

在 Azure 入口網站中所列的叢集狀態可協助您監視自動調整規模的活動。

![啟用背景工作節點負載為基礎的自動調整選項](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

所有您可能會看到叢集狀態訊息會說明下列清單中。

| 叢集狀態 | 說明 |
|---|---|
| 執行中 | 叢集運作正常。 所有先前的自動調整活動已順利完成。 |
| 正在更新  | 正在更新叢集中自動調整規模設定。  |
| HDInsight 設定  | 叢集相應增加或相應減少作業正在進行中。  |
| 更新錯誤  | HDInsight 會自動調整設定更新期間發生問題。 客戶可以選擇重試更新，或停用自動調整。  |
| 錯誤  | 有誤的叢集，並不是可使用。 刪除此叢集，然後建立新的帳戶。  |

若要檢視目前的節點數目，在叢集中，請前往**叢集大小**上的圖表**概觀**頁面上，為您的叢集，或按一下**叢集大小**下**設定**。

### <a name="operation-history"></a>作業歷程記錄

您可以檢視叢集計量的叢集相應增加和相應減少歷程的記錄。 您也可以透過前一天、 週或其他一段時間，列出所有的調整動作。

選取 **度量**下方**監視**。 然後按一下 **新增計量**並**數字的使用中工作者**從**計量**下拉式方塊。 按一下右上方，若要變更時間範圍中的按鈕。

![啟用背景工作節點排程為基礎的自動調整選項](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>後續步驟

* 閱讀[調整規模最佳做法](hdinsight-scaling-best-practices.md)，了解手動調整叢集規模的最佳做法
