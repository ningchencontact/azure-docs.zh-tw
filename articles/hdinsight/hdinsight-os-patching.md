---
title: 設定作業系統修補排程以 Linux 為基礎的 HDInsight 叢集-Azure
description: 了解如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657044"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>設定作業系統修補排程以 Linux 為基礎的 HDInsight 叢集 

> [!IMPORTANT]
> Ubuntu 映像都會變成可供發行的三個月內新的 Azure HDInsight 叢集建立的。 截至年 1 月 2019年執行的叢集不是自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。 新建立的叢集一律會有最新可用的更新，包括最新的安全性修補程式。

有時候，您必須重新啟動虛擬機器 (Vm) 在 HDInsight 叢集中安裝重要的安全性修補程式。

藉由使用本文中所述的指令碼動作，您可以修改作業系統修補排程，如下所示：

1. 安裝所有更新，或都安裝核心 + 安全性更新或核心更新。
2. 請不要立即重新啟動，或排程在 VM 上的重新啟動。

> [!NOTE]  
> 這篇文章中所述的指令碼動作只會使用 2016 年 8 月 1 日之後建立的以 Linux 為基礎的 HDInsight 叢集。 修補程式重新啟動 Vm 之後，才是有效的。
> 指令碼動作不會自動套用更新的所有未來的更新週期。 執行指令碼每次新的更新必須套用安裝的更新，然後再重新啟動 VM。

## <a name="add-information-to-the-script"></a>將資訊新增至指令碼

使用指令碼需要下列資訊：

- 安裝-更新-排程的重新啟動指令碼位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight 會使用此 URI 來尋找及在叢集中的所有 Vm 上執行指令碼。 此指令碼會提供選項來安裝更新和重新啟動 VM。
  
- 排程重新開機指令碼位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight 會使用此 URI 來尋找及在叢集中的所有 Vm 上執行指令碼。 此指令碼會重新啟動 VM。
  
- 指令碼會套用到叢集節點類型是前端節點、 背景工作節點和 zookeeper。 指令碼套用至叢集中的所有節點類型。 如果指令碼不會套用至節點類型，就不會更新或重新啟動該節點類型的 Vm。

- 安裝-更新-排程的重新開機指令碼接受兩個數值參數：

    | 參數 | 定義 |
    | --- | --- |
    | 核心只安裝更新 / 安裝所有更新/都安裝核心 + 安全性只會更新|0、 1 或 2。 0 值只會安裝核心更新。 值為 1 會安裝所有更新，和 2 的都安裝只有核心 + 安全性更新。 如果未不提供任何參數，則預設值為 0。 |
    | 未重新開機/啟用排程重新開機/啟用立即重新開機 |0、 1 或 2。 值為 0 會停用重新啟動。 值為 1 會啟用排程重新啟動，和 2，可讓 「 立即重新啟動。 如果未不提供任何參數，則預設值為 0。 使用者必須變更輸入的參數 1，以輸入參數 2。 |
   
 - 排程重新開機指令碼會接受一個數值參數：

    | 參數 | 定義 |
    | --- | --- |
    | 啟用排程重新開機/啟用立即重新啟動 |1 或 2。 值為 1 可讓排程的重新啟動 （排程以 12-24 個小時為單位）。 值為 2 可讓您立即重新啟動 （以 5 分鐘為單位）。 如果未不指定任何參數，則預設值為 1。 |  

> [!NOTE]
> 為持續性套用至現有的叢集之後，您必須將標記指令碼。 否則，透過調整作業所建立的新節點會使用預設的修補排程。 如果您套用指令碼做為叢集建立程序的一部分時，它已自動保存。


## <a name="next-steps"></a>後續步驟

如需使用指令碼動作的特定步驟，請參閱下列各節[使用指令碼動作來自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md):

* [在建立叢集期間使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [將指令碼動作套用到執行中的叢集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
