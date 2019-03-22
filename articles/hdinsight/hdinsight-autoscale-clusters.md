---
title: 自動調整 Azure HDInsight 叢集 （預覽）
description: 使用 HDInsight 自動調整功能自動調整叢集規模
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 28f04f5ab3cf8310a6ee3828405910d34b31591b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227571"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>自動調整 Azure HDInsight 叢集 （預覽）

>[!Important]
>HDInsight 自動調整功能目前為預覽狀態。 請傳送電子郵件給hdiautoscalepm@microsoft.com能夠啟用您的訂用帳戶的自動調整。

Azure HDInsight 的叢集自動調整功能可根據預先定義範圍內的負載，自動相應增加和減少叢集中背景工作節點的數目。 在建立新的 HDInsight 叢集時，可以設定最小和最大的背景工作節點數目。 系統在自動調整後會監視分析負載的資源需求，並據以相應增加或減少背景工作節點數目。 這項功能不需額外付費。

## <a name="getting-started"></a>開始使用

### <a name="create-a-cluster-with-the-azure-portal"></a>使用 Azure 入口網站建立叢集

> [!Note]
> 目前只有 Azure HDInsight Hive、MapReduce 和 Spark 叢集 3.6 版支援自動調整。

若要啟用自動調整功能，執行下列一般叢集建立程序的一部分：

1. 選取 [自訂 (大小、設定、應用程式)]，而非 [快速建立]。
2. 在 [自訂] 步驟 5 [(叢集大小)]上，勾選 [背景工作節點自動調整] 核取方塊。
3. 下列屬性輸入所需的值：  

    * 初始**背景工作節點數目**。  
    * 背景工作節點**最小**數目。  
    * 背景工作節點**最大**數目。  

![啟用背景工作節點自動調整選項](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

背景工作節點的初始數目必須介於最小值和最大值 (含) 之間。 此值會定義叢集建立時的初始大小。 背景工作節點的最小數目必須大於零。

在您為每個節點類型選擇 VM 類型之後，將能看到整個叢集的預估成本範圍。 然後您可以調整這些設定，以符合您的預算。

您的訂用帳戶擁有每個區域的容量配額。 您前端節點的核心總數與背景工作節點的最大數目結合之後，不能超過容量配額。 但是這個配額是軟性限制；您一律可以建立支援票證，輕鬆增加配額。

> [!Note]  
> 如果您超過總核心配額限制，就會收到錯誤訊息，說明「最大節點數超出此區域的可用節點數，請選擇其他區域或聯絡支援人員增加配額」。

如需使用 Azure 入口網站建立 HDInsight 叢集的詳細資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用 Resource Manager 範本建立叢集

若要使用 Azure Resource Manager 範本建立 HDInsight 叢集，請將 `autoscale` 節點新增至 `computeProfile` > `workernode` 區段，其 `minInstanceCount` 與 `maxInstanceCount` 屬性如下方 json 程式碼片段所示。

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>針對正在執行的叢集啟用和停用自動調整

您可以啟用或停用自動調整在 Azure 入口網站的 於 2019 年 1 月 1 日之後建立的 HDInsight 叢集。

## <a name="monitoring"></a>監視

您可以檢視叢集計量的叢集相應增加和相應減少歷程的記錄。 您也可以列出過去一天、一週或更長期間的所有規模調整動作。

## <a name="how-it-works"></a>運作方式

### <a name="metrics-monitoring"></a>計量監視

自動調整會持續監視叢集，並收集下列計量：

1. **擱置中的 CPU 總計**：開始執行所有擱置中容器時所需的核心總數。
2. **擱置中的記憶體總計**：開始執行所有擱置中容器時所需的記憶體總計 (MB)。
3. **可用的 CPU 總計**：作用中背景工作節點上所有未使用核心的總和。
4. **可用的記憶體總計**：作用中背景工作節點上所有未使用記憶體的總和 (MB)。
5. **每個節點的已使用記憶體**：背景工作節點上的負載。 已使用 10 GB 記憶體的背景工作節點會被視為所承受的負載高於已使用 2 GB 記憶體的背景工作節點。
6. **每個節點的應用程式主機數目**：背景工作節點上執行的應用程式主機 (AM) 容器數目。 裝載兩個的上午容器在背景工作節點會被視為比裝載零上午容器在背景工作節點更重要的。

系統每隔 60 秒就會檢查上述計量。 自動調整規模將根據這些計量的向上延展和向下調整決策。

### <a name="cluster-scale-up"></a>叢集相應增加

當偵測到下列條件時，自動調整規模就會發出相應增加要求：

* 擱置中的 CPU 總計大於可用的 CPU 總計超過 1 分鐘。
* 擱置中的記憶體總計大於可用的記憶體總計超過 1 分鐘。

我們會計算新的背景工作角色節點數目所需符合目前的 CPU 和記憶體需求，然後發出將加入的新背景工作節點數目相應增加要求。

### <a name="cluster-scale-down"></a>向下調整叢集

當偵測到下列條件時，自動調整規模就會發出相應減少要求：

* 擱置中的 CPU 總計小於可用的 CPU 總計超過 10 分鐘。
* 擱置中的記憶體總計小於可用的記憶體總計超過 10 分鐘。

根據每個節點和目前的 CPU 和記憶體需求的上午容器的數目，自動調整規模將會發出要求，若要移除特定數目的節點，指定哪些節點可移除的潛在候選項目。 根據預設，一次循環中將會移除兩個節點。

## <a name="next-steps"></a>後續步驟

* 閱讀[調整規模最佳做法](hdinsight-scaling-best-practices.md)，了解手動調整叢集規模的最佳做法
