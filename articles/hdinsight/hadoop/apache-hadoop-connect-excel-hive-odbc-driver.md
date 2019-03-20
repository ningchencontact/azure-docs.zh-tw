---
title: 使用 Hive ODBC 驅動程式將 Excel 連線到 Apache Hadoop - Azure HDInsight
description: 了解如何設定和使用 Excel 的 Microsoft Hive ODBC 驅動程式，從 Microsoft Excel 查詢 HDInsight 叢集中的資料。
keywords: hadoop excel, hive excel, hive odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: hrasheed
ms.openlocfilehash: 3a47b18051036e925e54b9507bf2cb4e40aad844
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202517"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 Azure HDInsight 中的 Apache Hadoop

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft 的巨量資料解決方案會將 Microsoft 商業智慧 (BI) 元件與已部署於 Azure HDInsight 中的 Apache Hadoop 叢集整合。 舉例來說，此整合可讓您使用 Microsoft Hive 開放式資料庫連線能力 (ODBC) 驅動程式，將 Excel 連線到 HDInsight 中 Hadoop 叢集的 Hive 資料倉儲。

还可以使用用于 Excel 的 Microsoft Power Query 外接程序从 Excel 连接与 HDInsight 群集和其他数据源（包括其他非 HDInsight Hadoop 群集）关联的数据。 如需安裝和使用 Power Query 的相關資訊，請參閱[使用 Power Query 將 Excel 連接到 HDInsight][hdinsight-power-query]。


## <a name="prerequisites"></a>必要條件

開始閱讀本文之前，您必須有下列各項：

* HDInsight Hadoop 叢集。 若要建立，請參閱[開始使用 Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)。
* 具有 Office 2010 專業增強版或更新版本，或是 Excel 2010 或更新版本的工作站。

## <a name="install-microsoft-hive-odbc-driver"></a>安裝 Microsoft Hive ODBC 驅動程式
下載並安裝與您將要使用 ODBC 驅動程式的應用程式版本相符的 [Microsoft Hive ODBC 驅動程式][hive-odbc-driver-download]版本。  本教學課程將針對 Office Excel 使用此驅動程式。

## <a name="create-apache-hive-odbc-data-source"></a>建立 Apache Hive ODBC 資料來源
下列步驟將說明如何建立 Hive ODBC 資料來源。

1. 從 Windows 中，瀏覽至 [開始] > [Windows 系統管理工具] > [ODBC 資料來源 (32 位元)/(64 位元)]。  這樣會開啟 [ODBC 資料來源管理員] 視窗。
   
    ![OBDC 資料來源管理員](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "使用 ODBC 資料來源管理員設定 DSN")

2. 從 [使用者 DSN] 索引標籤，選取 [新增] 以開啟 [建立新資料來源] 視窗。

3. 選取 [Microsoft Hive ODBC 驅動程式]，然後選取 [完成] 以開啟 [Microsoft Hive ODBC 驅動程式 DSN 設定] 視窗。

4. 輸入或選取下列值：
   
   | 屬性 | 描述 |
   | --- | --- |
   |  資料來源名稱 |為資料來源指定名稱 |
   |  主機 |輸入 &lt;HDInsightClusterName&gt;.azurehdinsight.net。 例如，myHDICluster.azurehdinsight.net |
   |  Port |使用 <strong>443</strong> (此連接埠已從 563 變更為 443)。 |
   |  資料庫 |使用<strong>預設值</strong>。 |
   |  機制 |選取 [Azure HDInsight 服務] |
   |  使用者名稱 |輸入 HDInsight 叢集 HTTP 使用者的使用者名稱。 預設的使用者名稱為 <strong>admin</strong>。 |
   |  密碼 |輸入 HDInsight 叢集使用者的密碼。 |

   
5. 選用：選取 [進階選項...]  
   
   | 參數 | 描述 |
   | --- | --- |
   |  使用原生查詢 |選取此選項時，ODBC 驅動程式不會嘗試將 TSQL 轉換為 HiveQL。 只有在百分之百確定您所提交的是純 HiveQL 陳述式時，才應使用此選項。 連接到 SQL Server 或 Azure SQL Database 時，您應將它保留為未勾選。 |
   |  每個區塊擷取的資料列 |在擷取大量記錄時，可能必須調整此參數，以確保最佳效能。 |
   |  默认字符串列长度、二进制列长度、十进制列小数位数 |資料類型的長度和精確度可能會影響傳回資料的方式。 如果失去精確度且 (或) 發生截斷狀況，會傳回不正確的資訊。 |

    ![進階選項](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "進階 DSN 設定選項")

5. 選取 [測試] 以測試資料來源。 正確設定資料來源時，測試結果就會顯示**成功！**。  

6. 選取 [確定] 以關閉 [測試] 視窗。  

7. 選取 [確定] 以關閉 [Microsoft Hive ODBC 驅動程式 DSN 設定] 視窗。  

8. 選取 [確定] 以關閉 [ODBC 資料來源管理員] 視窗。  

## <a name="import-data-into-excel-from-hdinsight"></a>從 HDInsight 將資料匯入 Excel 中
下列步驟將說明如何使用您在上一節中建立的 ODBC 資料來源，將資料從 Hive 資料表匯入 Excel 活頁簿中。

1. 在 Excel 中開啟新的或現有的活頁簿。

2. 從 [資料] 索引標籤，瀏覽至 [取得資料] > [從其他來源] > [從 ODBC] 以啟動 [從 ODBC] 視窗。
   
    ![開啟資料連線精靈](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "開啟資料連線精靈")

3. 從下拉式清單中，選取您在上一節建立的資料來源名稱，然後選取 [確定]。

4. 輸入 Hadoop 使用者名稱 (預設名稱為 admin) 和密碼，然後選取 [連線] 以開啟 [導覽器] 視窗。

5. 從 [導覽器] 中，瀏覽至 [HIVE] > [預設值] > [hivesampletable]，然後選取 [載入]。 資料需要一些時間才會匯入至 Excel。

    ![HDInsight Hive ODBC 導覽器](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "開啟資料連接精靈")

## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 Microsoft Hive ODBC 驅動程式將 HDInsight 服務中的資料擷取至 Excel。 同樣地，您也可以將 HDInsight 服務中的資料擷取至 SQL Database。 此外也可以將資料上傳至 HDInsight 服務。 若要深入了解，請參閱：

* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Apache Hive 資料視覺化](apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 將互動式查詢 Hive 資料視覺化](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢](./../hdinsight-connect-hive-zeppelin.md)。
* [使用 Power Query 將 Excel 連線到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [將資料上傳至 HDInsight](./../hdinsight-upload-data.md)。

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698


