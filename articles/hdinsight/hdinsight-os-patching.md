---
title: 以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程 - Azure
description: 了解如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程。
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: cfbd68e66730fc338130bc16849fe0b2f4abd6be
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244415"
---
# <a name="os-patching-for-hdinsight"></a>HDInsight 的作業系統修補 

> [!IMPORTANT]
> Ubuntu 映像在發行後的 3 個月內就會變成可用於建立新的 HDInsight 叢集。 截至 2019 年 1 月為止，執行中的叢集都**不會**自動修補。 客戶必須使用指令碼動作或其他機制修補執行中的叢集。 新建立的叢集一律會有最新可用的更新，包括最新的安全性修補程式。

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>如何為以 Linux 為基礎的 HDInsight 叢集設定作業系統修補排程
HDInsight 叢集中的虛擬機器有時候需要重新開機，以便系統可以安裝重要的安全性修補程式。 

您可以使用本文所述的指令碼動作來修改作業系統修補排程，如下所示︰
1. 安裝完整的 OS 更新或安裝僅限安全性更新
2. 重新啟動 VM

> [!NOTE]  
> 此指令碼動作只適用於在 2016 年 8 月 1 日之後建立的以 Linux 為基礎的 HDInsight 叢集。 VM 重新開機後，修補才會生效。 此指令碼將不會自動套用更新的所有未來的更新週期。 執行指令碼每次新的更新必須套用以安裝更新並重新啟動 VM。

## <a name="how-to-use-the-script"></a>如何使用指令碼 

使用此指令碼時需要下列資訊︰
1. 指令碼位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh。HDInsight 會使用此 URI 在叢集中的所有虛擬機器上尋找和執行指令碼。
  
2. 指令碼會套用到的叢集節點類型︰headnode、workernode、zookeeper。 此指令碼必須套用至叢集中的所有節點類型。 如果它不會套用至節點類型，則該節點類型的虛擬機器將不會更新。


3.  參數：此指令碼會接受一個數值參數：

    | 參數 | 定義 |
    | --- | --- |
    | 安裝完整作業系統更新/安裝只有安全性更新 |0 或 1。 值為 0 在 1 會安裝完整的 OS 更新時，才會安裝安全性更新。 如果沒有參數提供預設值為 0。 |

> [!NOTE]  
> 在對現有叢集套用此指令碼時，您必須將其標示為持續性。 否則，透過調整作業所建立的新節點會使用預設的修補排程。  如果您在進行叢集建立程序時套用指令碼，該指令碼會自動成為持續性狀態。


## <a name="next-steps"></a>後續步驟

如需使用指令碼動作的特定步驟，請參閱[使用指令碼動作來自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)中的以下章節：

* [在建立叢集期間使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [將指令碼動作套用到執行中的叢集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
