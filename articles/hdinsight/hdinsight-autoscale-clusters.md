---
title: 自動調整 Azure HDInsight 叢集規模
description: 使用 HDInsight 自動調整功能自動調整叢集規模
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: 043c83e2039d87b1650ba17f770ce16a2ad2c13d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811157"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自動調整 Azure HDInsight 叢集規模

Azure HDInsight 的叢集自動調整功能可根據預先定義範圍內的負載，自動相應增加和減少叢集中背景工作節點的數目。 在建立新的 HDInsight 叢集時，可以設定最小和最大的背景工作節點數目。 系統在自動調整後會監視分析負載的資源需求，並據以相應增加或減少背景工作節點數目。 這項功能不需額外付費。

## <a name="getting-started"></a>開始使用

### <a name="create-cluster-with-azure-portal"></a>使用 Azure 入口網站建立叢集

> [!Note]
> 目前只有 Azure HDInsight Hive、MapReduce 和 Spark 叢集 3.6 版支援自動調整。

請依照[使用 Azure 入口網站在 HDInsight 建立以 Linux 為基礎的叢集](hdinsight-hadoop-create-linux-clusters-portal.md)，然後在您執行到步驟 5 時，請如以下所示，在 [叢集大小] 選取 [背景工作節點自動調整 (預覽)]。 

![啟用背景工作節點自動調整選項](./media/hdinsight-autoscale-clusters/worker-node-autoscale-option.png)

勾選此選項後，就可以指定：

* 背景工作節點的初始數目
* 背景工作節點的最少數目
* 背景工作節點的最多數目

背景工作節點的初始數目必須介於最小值和最大值 (含) 之間。 此值會定義叢集建立時的初始大小。 背景工作節點的最小數目必須大於零。

在您為每個節點類型選擇 VM 類型之後，將能看到整個叢集的預估成本範圍。 然後您可以調整這些設定，以符合您的預算。

您的訂用帳戶擁有每個區域的容量配額。 您前端節點的核心總數與背景工作節點的最大數目結合之後，不能超過容量配額。 但是這個配額是軟性限制；您一律可以建立支援票證，輕鬆增加配額。

> [!Note]
> 如果您超過總核心配額限制，就會收到錯誤訊息，說明「最大節點數超出此區域的可用節點數，請選擇其他區域或聯絡支援人員增加配額」。

### <a name="create-cluster-with-an-resource-manager-template"></a>使用 Resource Manager 範本建立叢集

當您使用 Resource Manager 範本建立 HDInsight 叢集時，您需要在 “computeProfile” “worker node” 區段中新增下列設定：

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

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>針對正在執行的叢集啟用和停用自動調整

在個人預覽版期間，不支援針對正在執行的叢集啟用自動調整。 必須在建立叢集時啟用。

在個人預覽版中，不支援針對正在執行的叢集停用自動調整或修改自動調整設定。 您必須刪除叢集並建立新叢集，才能刪除或修改設定。

## <a name="monitoring"></a>監視

您可以檢視叢集計量中包含的叢集相應增加和相應減少記錄。 您可以列出過去一天、一週或更長期間的所有規模調整動作。

## <a name="how-it-works"></a>運作方式

### <a name="metrics-monitoring"></a>計量監視

自動調整會持續監視叢集，並收集下列計量：

1. **擱置中的 CPU 總計**：開始執行所有擱置中容器時所需的核心總數。
2. **擱置中的記憶體總計**：開始執行所有擱置中容器時所需的記憶體總計 (MB)。
3. **可用的 CPU 總計**：作用中背景工作節點上所有未使用核心的總和。
4. **可用的記憶體總計**：作用中背景工作節點上所有未使用記憶體的總和 (MB)。
5. **每個節點的已使用記憶體**：背景工作節點上的負載。 已使用 10 GB 記憶體的背景工作節點會被視為所承受的負載高於已使用 2 GB 記憶體的背景工作節點。
6. **每個節點的應用程式主機數目**：背景工作節點上執行的應用程式主機 (AM) 容器數目。 裝載 2 個 AM 容器的背景工作節點會被視為其重要程度高於裝載 0 個 AM 容器的背景工作節點。

系統每隔 60 秒就會檢查上述計量。 自動調整將依據這些計量決定要進行相應增加或相應減少。

### <a name="cluster-scale-up"></a>叢集相應增加

當偵測到下列情況時，自動調整將會發出相應增加要求：

* 擱置中的 CPU 總計大於可用的 CPU 總計超過 1 分鐘。
* 擱置中的記憶體總計大於可用的記憶體總計超過 1 分鐘。

我們會計算出需要 N 個新的背景工作節點才能符合目前的 CPU 和記憶體需求，然後發出要求 N 個新背景工作節點的相應增加要求。

### <a name="cluster-scale-down"></a>叢集相應減少

當偵測到下列情況時，自動調整將會發出相應減少要求：

* 擱置中的 CPU 總計小於可用的 CPU 總計超過 10 分鐘。
* 擱置中的記憶體總計小於可用的記憶體總計超過 10 分鐘。

自動調整將會根據每個節點的 AM 容器數和目前的 CPU 與記憶體需求，發出移除 N 個節點的要求，並指定哪些節點可能可以移除。 根據預設，一次循環中將會移除兩個節點。

## <a name="next-steps"></a>後續步驟

* 閱讀[調整規模最佳做法](hdinsight-scaling-best-practices.md)，了解手動調整叢集規模的最佳做法
