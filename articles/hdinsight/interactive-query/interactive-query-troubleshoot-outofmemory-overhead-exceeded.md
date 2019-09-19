---
title: Apache Hive 中的聯結會導致 Azure HDInsight 中的 OutOfMemory 錯誤
description: 處理 OutOfMemory 錯誤「GC 負擔限制超過錯誤」
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: e1f41b6e1e5f51cb7e6e0af1e99184cdfbd373e1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091414"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>案例：Apache Hive 中的聯結會導致 Azure HDInsight 中的 OutOfMemory 錯誤

本文說明在 Azure HDInsight 叢集中使用互動式查詢元件時，針對問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache Hive 聯結的預設行為是將資料表的整個內容載入記憶體中，以便在不需要執行對應/減少步驟的情況下執行聯結。 如果 Hive 資料表太大而無法放入記憶體中，則查詢可能會失敗。

## <a name="cause"></a>原因

在具有足夠大小的 hive 中執行聯結時，會發生下列錯誤：

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>解析度

藉由設定下列 Hive 設定值，防止 Hive 將資料表載入記憶體中的聯結（改為執行對應/減少步驟）：

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>後續步驟

如果設定此值無法解決您的問題，請造訪下列其中一項 。

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
