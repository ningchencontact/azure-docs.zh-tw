---
title: 以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程 - Azure
description: 了解如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程。
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 3fad8869a31688e9e2413abb350eccf1f871f7dd
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330711"
---
# <a name="os-patching-for-hdinsight"></a>HDInsight 的作業系統修補 

> [!IMPORTANT]
> Ubuntu 映像都會變成可供發行的三個月內的新 HDInsight 叢集建立的。 截至 2019 年 1 月為止，執行中的叢集都**不會**自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。 新建立的叢集一律會有最新可用的更新，包括最新的安全性修補程式。

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程
HDInsight 叢集中的虛擬機器有時候需要重新開機，以便系統可以安裝重要的安全性修補程式。 

您可以使用本文中所述的指令碼動作，來修改作業系統修補排程，如下所示：
1. 安裝所有更新或都安裝核心 + 只有安全性更新或只都安裝核心更新。
2. 立即重新啟動或排程在 VM 重新開機。

> [!NOTE]  
> 這些指令碼動作只適用於 2016 年 8 月 1 日之後建立的以 Linux 為基礎的 HDInsight 叢集。 VM 重新開機後，修補才會生效。 這些指令碼將不會自動套用更新的所有未來的更新週期。 執行指令碼每次新的更新必須套用以安裝更新並重新啟動 VM。

## <a name="how-to-use-the-script"></a>如何使用指令碼 

使用此指令碼需要下列資訊：
1. 安裝-更新-排程的重新啟動指令碼位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight 會使用此 URI 在叢集中的所有虛擬機器上尋找和執行指令碼。 此指令碼會提供選項來安裝更新和重新啟動 VM。
  
2. 排程重新開機指令碼位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight 會使用此 URI 在叢集中的所有虛擬機器上尋找和執行指令碼。 此指令碼會重新啟動 VM。
  
3. 指令碼會套用到的叢集節點類型︰headnode、workernode、zookeeper。 此指令碼必須套用至叢集中的所有節點類型。 如果它不會套用至節點類型，然後該節點類型的虛擬機器將不會更新或重新啟動。

4. 參數：安裝-更新-排程的重新開機指令碼接受兩個數值參數：

    | 參數 | 定義 |
    | --- | --- |
    | 核心只安裝更新 / 安裝所有更新/都安裝核心 + 安全性只會更新 |0 或 1 或 2。 值為 0 會安裝核心更新，而 1 會安裝所有都更新，並安裝 2 個核心 + security 只會都更新。 如果未不提供任何參數，則預設值為 0。 |
    | 未重新開機/啟用排程重新開機/啟用立即重新開機 |0 或 1 或 2。 值為 0 會停用重新開機，而 1 會啟用排程重新開機和 2，可讓 「 立即重新啟動。 如果未不提供任何參數，則預設值為 0。 使用者必須輸入參數 1，以輸入參數 2。 |
   
 5. 參數：排程重新開機指令碼會接受一個數值參數：

    | 參數 | 定義 |
    | --- | --- |
    | 啟用排程重新開機/啟用立即重新啟動 |1 或 2。 值為 1 會啟用排程重新開機 （重新啟動會排定在接下來 12-24 小時） 而 2 啟用立即重新啟動 （以 5 分鐘為單位）。 如果未不提供任何參數，則預設值為 1。 |  

> [!NOTE] 
> 必須將指令碼標示為持續性時將套用至現有的叢集。 否則，透過調整作業所建立的新節點會使用預設的修補排程。  如果您在進行叢集建立程序時套用指令碼，該指令碼會自動成為持續性狀態。


## <a name="next-steps"></a>後續步驟

如需使用指令碼動作的特定步驟，請參閱下列各節[使用指令碼動作來自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md):

* [在建立叢集期間使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [將指令碼動作套用到執行中的叢集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
