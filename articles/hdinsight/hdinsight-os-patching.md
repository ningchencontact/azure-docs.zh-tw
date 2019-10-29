---
title: 為 Azure HDInsight 叢集設定作業系統修補排程
description: 了解如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: d0a490fd3b23c96923af10db3c1f9ee9ea0dfad5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044889"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程 

> [!IMPORTANT]
> Ubuntu 映射會在發行後的三個月內，提供新的 Azure HDInsight 叢集建立。 從2019年1月起，執行中的叢集不會自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。 新建立的叢集一律會有最新可用的更新，包括最新的安全性修補程式。

有時候，您必須重新開機 HDInsight 叢集中的虛擬機器（Vm），以安裝重要的安全性修補程式。

藉由使用本文所述的腳本動作，您可以修改 OS 修補排程，如下所示：

1. 安裝所有更新，或只安裝核心 + 安全性更新或核心更新。
2. 立即重新開機，或在 VM 上排程重新開機。

> [!NOTE]  
> 本文中所述的腳本動作僅適用于2016年8月1日之後建立的以 Linux 為基礎的 HDInsight 叢集。 只有在重新開機 Vm 之後，修補程式才會生效。
> 腳本動作不會針對所有未來的更新週期自動套用更新。 每次必須套用新的更新以安裝更新，然後重新開機 VM 時，執行腳本。

## <a name="add-information-to-the-script"></a>將資訊新增至腳本

使用腳本需要下列資訊：

- 安裝-更新-排程-重新開機腳本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight 會使用此 URI，在叢集中的所有 Vm 上尋找並執行腳本。 此腳本提供安裝更新並重新啟動 VM 的選項。
  
- 排程-重新開機腳本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight 會使用此 URI，在叢集中的所有 Vm 上尋找並執行腳本。 此腳本會重新開機 VM。
  
- 套用腳本的叢集節點類型為 [前端]、[workernode] 和 [zookeeper]。 將腳本套用至叢集中的所有節點類型。 如果腳本未套用至節點類型，則不會更新或重新開機該節點類型的 Vm。

- 安裝-更新-排程-重新開機腳本接受兩個數值參數：

    | 參數 | 定義 |
    | --- | --- |
    | 僅安裝核心更新/安裝所有更新/僅安裝核心 + 安全性更新|0、1或2。 值為0時，只會安裝核心更新。 值為1會安裝所有更新，而2只會安裝核心 + 安全性更新。 如果未提供任何參數，則預設值為0。 |
    | 無重新開機/啟用排程重新開機/啟用立即重新開機 |0、1或2。 值為0會停用重新開機。 值為1會啟用排程重新開機，2則可立即重新開機。 如果未提供任何參數，則預設值為0。 使用者必須將輸入參數1變更為輸入參數2。 |
   
 - 排程-重新開機腳本接受一個數值參數：

    | 參數 | 定義 |
    | --- | --- |
    | 啟用排程重新開機/啟用立即重新開機 |1或2。 值為1會啟用排程重新開機（排程時間為12-24 小時）。 值為2時，會立即重新開機（5分鐘）。 如果未指定參數，預設值為1。 |  

> [!NOTE]
> 將腳本套用至現有的叢集之後，您必須將它標示為已保存。 否則，透過調整作業所建立的新節點會使用預設的修補排程。 如果您將腳本套用為叢集建立程式的一部分，則會自動儲存。


## <a name="next-steps"></a>後續步驟

如需使用腳本動作的特定步驟，請參閱[使用腳本動作自訂以 Linux 為基礎的 HDInsight](hdinsight-hadoop-customize-cluster-linux.md)叢集中的下列各節：

* [在建立叢集期間使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [將指令碼動作套用到執行中的叢集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
