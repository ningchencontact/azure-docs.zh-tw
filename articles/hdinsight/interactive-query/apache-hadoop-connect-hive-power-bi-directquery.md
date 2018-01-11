---
title: "互動式查詢 Hive 與 Azure HDInsight 中的 Power BI 的資料視覺化 |Microsoft 文件"
description: "了解如何使用 Microsoft Power BI 以視覺化方式檢視處理 Azure HDInsight 登錄區的互動式查詢資料。"
keywords: "hdinsight hadoop hive，互動式查詢，互動式 hive，LLAP，directquery"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: jgao
ms.openlocfilehash: 290e600b7be4a6f9fb57afa50bb771e42e6a0624
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>使用 Microsoft Power BI 使用 Azure HDInsight 中直接查詢視覺化互動式查詢 hive 控制檔的資料

了解如何連線到 Azure HDInsight 互動式查詢叢集的 Microsoft Power BI 和視覺化使用直接查詢登錄區資料。 在本教學課程中，您可將資料從 hivesampletable Hive 資料表載入至 Power BI。 Hive 資料表包含某些行動電話使用量資料。 然後您可在世界地圖上繪製使用量資料：

![HDInsight Power BI 地圖報告](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

如需瞭解如何使用 ODBC 的 hive 控制檔的連接，請參閱[視覺化 hive 控制檔的資料，使用 Microsoft Power BI 使用 Azure HDInsight 中的 ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 

## <a name="prerequisites"></a>必要條件
在閱讀本文之前，您必須有下列各項：

* **HDInsight 叢集**。 叢集可以是新發行的互動式查詢叢集或 Hive 與 HDInsight 叢集。 如需建立叢集，請參閱[建立叢集](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)。
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**。 您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=45331)下載一份。

## <a name="load-data-from-hdinsight"></a>從 HDInsight 載入資料

所有 HDInsight 叢集都隨附 hivesampletable Hive 資料表。

1. 登入 Power BI Desktop。
2. 按一下 [首頁] 索引標籤，從 [外部資料] 功能區按一下 [取得資料]，然後選取 [更多]。

    ![HDInsight Power BI 開放式資料](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. 從**取得資料**窗格中，輸入**hdinsight** [搜尋] 方塊中。 如果您沒有看到**HDInsight 互動式查詢 (Beta)**，您需要更新您的 Power BI Desktop 的最新版本。
4. 按一下**HDInsight 互動式查詢 (Beta)**，然後按一下 **連接**。
5. 按一下**繼續**關閉**預覽連接器**警告對話方塊。
6. 從**HDInsight 互動式查詢**，選取或輸入下列資訊：

    - **伺服器**： 輸入的互動式查詢叢集名稱，例如*myiqcluster.azurehdinsight.net*。
    - **資料庫**： 此教學課程中，輸入**預設**。
    - **資料連線模式**： 在此教學課程中，選取**DirectQuery**。

    ![HDInsight 互動式查詢 power bi directquery 連接](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. 按一下 [SERVICEPRINCIPAL] 。
8. 輸入 HTTP 使用者認證，然後再按一下**確定**。  預設使用者名稱是**系統管理員**
9. 從左窗格中，選取**hivesampletale**，然後按一下 **負載**。

    ![HDInsight 互動式查詢 power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>顯現資料

從上一個程序繼續進行。

1. 從 [視覺效果] 窗格中選取 [地圖]。  它是地球圖示。

    ![HDInsight Power BI 自訂報告](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. 從 [欄位] 窗格選取 **country** 和 **devicemake**。 您可以看到地圖上繪製的資料。
3. 展開地圖。

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 Power BI 將 HDInsight 中的資料視覺化。  若要深入了解，請參閱下列文章：

* [將 Hive 資料視覺化與 Microsoft Power BI 使用 Azure HDInsight 中的 ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md)。 
* [使用 Zeppelin 在 Azure HDInsight 中執行 Hive 查詢](./../hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 將 Excel 連線到 Hadoop](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Hive 查詢](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)。
* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [將資料上傳至 HDInsight](./../hdinsight-upload-data.md)。
