---
title: 在 Azure HDInsight 中使用 Power BI 將互動式查詢 Hive 資料視覺化
description: 使用 Microsoft Power BI 將 Azure HDInsight 的互動式查詢 Hive 資料視覺化
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: fb4e16c8be5344c5b9947758b6a09845b470196d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800998"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>在 Azure HDInsight 中使用直接查詢藉由 Microsoft Power BI 將互動式查詢 Apache Hive 資料視覺化

本文說明如何將 Microsoft Power BI 連線至 Azure HDInsight 互動式查詢叢集，然後使用直接查詢將 Apache Hive 資料視覺化。 提供的範例將資料從`hivesampletable`至 Power BI 的 Hive 資料表。 `hivesampletable` Hive 資料表包含某些行動電話使用方式資料。 然後您可在世界地圖上繪製使用量資料：

![HDInsight Power BI 地圖報告](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

您可以利用 [Apache Hive ODBC 驅動程式](../hadoop/apache-hadoop-connect-hive-power-bi.md)，透過 Power BI Desktop 中的一般 ODBC 連接器執行匯入作業。 不過，對於 BI 工作負載則不建議使用此方式，因為 Hive 查詢引擎具有非互動式的本質。 為發揮其效能，[HDInsight 互動式查詢連接器](./apache-hadoop-connect-hive-power-bi-directquery.md)和 [HDInsight Apache Spark 連接器](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)會是較好的選擇。

## <a name="prerequisites"></a>必要條件
在閱讀本文之前，您必須有下列各項：

* **HDInsight 叢集**。 此叢集可以是含有 Apache Hive 的 HDInsight 叢集或新發行的互動式查詢叢集。 如需建立叢集，請參閱[建立叢集](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** 。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=45331)下載一份。

## <a name="load-data-from-hdinsight"></a>從 HDInsight 載入資料

`hivesampletable`隨附所有 HDInsight 叢集的 Hive 資料表。

1. 啟動 Power BI Desktop。

2. 從功能表列中，瀏覽至**首頁** > **取得資料** > **更多...** .

    ![HDInsight Power BI 開放式資料](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. 從**取得資料** 視窗中，輸入**hdinsight**在搜尋方塊中。  

4. 從搜尋結果中，選取**HDInsight 互動式查詢**，然後選取**Connect**。  如果您沒有看到**HDInsight 互動式查詢**，您需要將 Power BI Desktop 更新為最新版本。

5. 選取 **繼續**以關閉**連接到第三方服務**對話方塊。

6. 在 [ **HDInsight 互動式查詢**] 視窗中，輸入下列資訊，然後選取**確定**:

    |屬性 | 值 |
    |---|---|
    |伺服器 |輸入叢集名稱，例如*myiqcluster.azurehdinsight.net*。|
    |資料庫 |請輸入**預設**這篇文章。|
    |資料連線模式 |選取  **DirectQuery**這篇文章。|

    ![HDInsight 互動式查詢 Power BI DirectQuery 連線](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. 輸入 HTTP 的認證，然後按**Connect**。 預設的使用者名稱為 **admin**。

8. 從**Navigator**視窗的左窗格中，選取**hivesampletale**。

9. 選取 **負載**從主視窗。

    ![HDInsight 互動式查詢 Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>將地圖上的資料視覺化

從上一個程序繼續進行。

1. 從 [視覺效果] 窗格中，選取**地圖**，地球圖示。 泛型的對應，則會出現在主視窗。

    ![HDInsight Power BI 自訂報告](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. 從 [欄位] 窗格選取 **country** 和 **devicemake**。 世界地圖與資料點在一段時間後就會出現在主視窗。

3. 展開地圖。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 Microsoft Power BI 將 HDInsight 中的資料視覺化。  如需資料視覺效果的詳細資訊，請參閱下列文章：

* [在 Azure HDInsight 中使用 ODBC 藉由 Microsoft Power BI 將 Apache Hive 資料視覺化](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 
* [使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 將 Excel 連線到 Apache Hadoop](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [將資料上傳至 HDInsight](./../hdinsight-upload-data.md)。
