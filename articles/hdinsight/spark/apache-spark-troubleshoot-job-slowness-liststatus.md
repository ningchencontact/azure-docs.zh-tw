---
title: Apache Spark 串流作業所花費的時間比平常更長, 無法在 Azure HDInsight 中處理
description: Apache Spark 串流作業所花費的時間比平常更長, 無法在 Azure HDInsight 中處理
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679428"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>案例:Apache Spark 串流作業所花費的時間比平常更長, 無法在 Azure HDInsight 中處理

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時, 疑難排解步驟和問題的可能解決方法。

## <a name="issue"></a>問題

您會發現有些 Apache Spark 串流作業的速度很慢, 或處理的時間比平常長。 對於 Spark 串流應用程式, 每個訊息批次都會對應至一個提交至 Spark 的作業。 如果作業通常需要 X 秒的時間來處理, 可能偶爾會花費比平常更多2-3 分鐘的時間。

## <a name="cause"></a>原因

其中一個可能的原因是, Apache Kafka 產生者需要2分鐘以上的時間才能完成寫出至 Kafka 叢集。 若要進一步偵測 Kafka 問題, 您可以將一些記錄新增至使用 Kafka 產生者的程式碼, 以傳送訊息, 並使其與 Kafka 叢集的記錄相互關聯。

另一個可能的原因是, 頻繁的 WASB 讀取和寫入可能會導致後續的微批次延遲。 `Filesystem.listStatus` 由於`O(n!)`移除重複專案的演算法, 的 WASB 的執行速度非常慢。 因為從`BlobListItem`到`FileMetadata`到`FileStatus`的額外轉換, 所以會使用太多記憶體。 例如, 此演算法需要超過30分鐘的時間來列出700000檔案。 因此, `ListBlobs`如果是透過 SparkSQL 每個微批次積極地呼叫, 它會導致後續的微批次落後, 使您在進行高排程延遲時所經歷的效果。 [此修補程式](https://issues.apache.org/jira/browse/HADOOP-15547)會修正此問題, 但如果您的環境中缺少`ListBlobs`此功能, 將會經歷高延遲。 此外, 即使您每小時刪除檔案, 後端中的清單還是必須反復查看所有資料列 (包括已刪除), 因為垃圾收集程式尚未完成。 雖然修補程式可能會解決某些問題, 但是垃圾收集問題可能仍會造成批次處理資料流程的延遲。

## <a name="resolution"></a>解決方法

套用[HADOOP-15547](https://issues.apache.org/jira/browse/HADOOP-15547)修正程式。 如果無法這麼做, 您可以使用 HDFS 做為檢查點位置。 將`checkpointDirectory`設定為類似的`hdfs://mycluster/checkpoint`:。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 藉由將 Azure 社區連接至適當的資源來改善客戶體驗: 解答、支援及專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
