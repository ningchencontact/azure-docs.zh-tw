---
title: 快速入門：在 Azure HDInsight 中執行 Apache Hive 查詢 - Apache Zeppelin
description: 了解如何使用 Apache Zeppelin 來執行 Apache Hive 查詢。
keywords: hdinsight,hadoop,hive,互動式查詢,LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: f4b8495646e83005dc48e8a729a0e5987b832721
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801015"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>快速入門：使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢

在此快速入門中，您將會了解如何使用 Apache Zeppelin 在 Azure HDInsight 中執行 [Apache Hive](https://hive.apache.org/) 查詢。 HDInsight 互動式查詢叢集包含 [Apache Zeppelin](https://zeppelin.apache.org/) Notebook，可供您用來執行互動式 Hive 查詢。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

HDInsight 互動式查詢叢集。 請參閱[建立叢集](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)以建立 HDInsight 叢集。  請務必選擇 [互動式查詢] 叢集類型。

## <a name="create-an-apache-zeppelin-note"></a>建立 Apache Zeppelin 記事

1. 在下列 URL (`https://CLUSTERNAME.azurehdinsight.net/zeppelin`) 中，將 `CLUSTERNAME` 取代為您的叢集名稱。 在網頁瀏覽器中輸入該 URL。

2. 輸入您的叢集登入使用者名稱與密碼。 透過 [Zeppelin] 頁面，您可以建立新的記事或開啟現有記事。 **HiveSample** 包含一些 Hive 查詢範例。  

    ![HDInsight 互動式查詢 Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. 選取 [建立新記事]。

4. 從 [建立新記事] 對話方塊，輸入或選取下列值：

    - 記事名稱：輸入記事的名稱。
    - 預設解譯器：從下拉式清單中選取 [jdbc]。

5. 選取 [建立記事]。

6. 在程式碼區段中輸入下列 Hive 查詢，然後按 **Shift + Enter**：

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight 互動式查詢 Zeppelin 執行查詢](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    第一行裡面的 **%jdbc(hive)** 陳述式會指示筆記本使用 Hive JDBC 解譯器。

    此查詢應該會傳回一個 Hive 資料表，其名稱為 hivesampletable。

    以下是您可以對 **hivesampletable** 執行的另外兩個 Hive 查詢：

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    相較於傳統的 Hive，查詢結果的傳回速度會快很多。

## <a name="clean-up-resources"></a>清除資源

完成此快速入門之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

若要刪除叢集，請參閱[使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢。 若要深入了解 Hive 查詢，下一篇文章將會說明如何使用 Visual Studio 來執行查詢。

> [!div class="nextstepaction"]
> [使用 Data Lake Tools for Visual Studio 連線至 Azure HDInsight 及執行 Apache Hive 查詢](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="see-also"></a>另請參閱

* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Apache Hive 資料視覺化](../hadoop/apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 將互動式查詢 Apache Hive 資料視覺化](./apache-hadoop-connect-hive-power-bi-directquery.md)。
* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連線到 HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 將 Excel 連線到 Apache Hadoop](../hadoop/apache-hadoop-connect-excel-power-query.md)。
* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)。
