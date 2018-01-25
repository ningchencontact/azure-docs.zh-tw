---
title: "使用 Azure Data Factory 中的 Spark 轉換資料 | Microsoft Docs"
description: "本教學課程提供逐步指示，說明如何使用 Azure Data Factory 中的 Spark 活動來轉換資料。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: c2ec6706c92f229bb05ad9a19246c6ffe5f615c9
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>使用 Azure Data Factory 中的 Spark 活動來轉換雲端中的資料
在本教學課程中，您會使用 Azure 入口網站建立 Data Factory 管線，以使用 Spark 活動和隨選 HDInsight 連結服務來轉換資料。 您會在本教學課程中執行下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。 
> * 建立具有 Spark 活動的管線
> * 觸發管線執行
> * 監視管道執行。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (正式推出版本 (GA))，請參閱 [Data Factory 第 1 版文件](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>先決條件
* **Azure 儲存體帳戶**。 您需要建立 python 指令碼和輸入檔案，並上傳至 Azure 儲存體。 spark 程式的輸出會儲存在這個儲存體帳戶中。 隨選 Spark 叢集與其主要儲存體是使用相同的儲存體帳戶。  
* **Azure PowerShell**(英文)。 遵循[如何安裝並設定 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的指示。


### <a name="upload-python-script-to-your-blob-storage-account"></a>將 python 指令碼上傳至 Blob 儲存體帳戶
1. 使用下列內容建立名為 **WordCount_Spark.py** 的 python 檔案： 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. 以您的 Azure 儲存體帳戶名稱取代 **&lt;storageAccountName&gt;**。 然後儲存檔案。 
3. 在 Azure Blob 儲存體中，建立名為 **adftutorial** 的容器 (如果不存在)。 
4. 建立名為 **spark** 的資料夾。
5. 在 **spark** 資料夾下，建立名為 **script** 的子資料夾。 
6. 將 **WordCount_Spark.py** 檔案上傳至 **script** 子資料夾。 


### <a name="upload-the-input-file"></a>上傳輸入檔案
1. 建立名為 **minecraftstory.txt** 的檔案並填入一些文字。 Spark 程式會計算這段文字中的字數。 
2. 在 `spark` 資料夾中建立名為 `inputfiles` 的子資料夾。 
3. 將 `minecraftstory.txt` 上傳至 `inputfiles` 子資料夾。 

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 按一下左邊功能表上的 [新增]、[資料 + 分析]，再按一下 [Data Factory]。 
   
   ![新增->DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面中，輸入 **ADFTutorialDataFactory** 作為 [名稱]。 
      
     ![新增資料處理站頁面](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 **全域唯一的**。 如果您在 [名稱] 欄位看到下列錯誤，請變更資料處理站的名稱 (例如 yournameADFTutorialBulkCopyDF)。 請參閱 [Data Factory - 命名規則](naming-rules.md)一文，以了解 Data Factory 成品的命名規則。
  
     ![名稱無法使用 - 錯誤](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. 選取您要在其中建立資料處理站的 Azure **訂用帳戶**。 
4. 針對 [資源群組]，請執行下列其中一個步驟︰
     
      - 選取 [使用現有的] ，然後從下拉式清單選取現有的資源群組。 
      - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
      本快速入門的某些步驟是假設您使用 **ADFTutorialResourceGroup** 作為資源群組名稱。 若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
4. 對 [版本] 選取 [V2 (預覽)]。
5. 選取 Data Factory 的 [位置]  。 目前，Data Factory V2 只允許您在美國東部、美國東部 2 和西歐區域中建立資料處理站。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。
6. 選取 [釘選到儀表板]。     
7. 按一下頁面底部的 [新增] 。
8. 在儀表板上，您會看到狀態如下的下列圖格︰**正在部署資料處理站**。 

    ![部署資料處理站圖格](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. 建立完成之後，您會看到如圖中所示的 [Data Factory] 頁面。
   
    ![Data Factory 首頁](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. 按一下 [編寫與監視] 圖格，以在另一個索引標籤中啟動 Data Factory 使用者介面應用程式。

## <a name="create-linked-services"></a>建立連結的服務
在本節中，您會撰寫兩個連結服務： 
    
- 將 Azure 儲存體帳戶連結至資料處理站的 **Azure 儲存體連結服務**。 隨選 HDInsight 叢集會使用此儲存體。 它也包含要執行的 Spark 指令碼。 
- **隨選 HDInsight 連結服務**。 Azure Data Factory 會自動建立 HDInsight 叢集、執行 Spark 程式，然後刪除已閒置一段預先設定時間的 HDInsight 叢集。 

### <a name="create-an-azure-storage-linked-service"></a>建立 Azure 儲存體連結服務

1. 在 [開始使用] 頁面中，切換至左面板中的 [編輯] 索引標籤，如下圖所示： 

    ![建立管線圖格](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. 按一下視窗底部的 [連線]，然後按一下 [+ 新增]。 

    ![新增連線按鈕](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. 在 [新增連結服務] 視窗中，選取 [Azure Blob 儲存體]，然後按一下 [繼續]。 

    ![選取 Azure Blob 儲存體](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. 選取您的 [Azure 儲存體帳戶名稱]，然後按一下 [儲存]。 

    ![指定 Blob 儲存體帳戶](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>建立隨選 HDInsight 連結服務

1. 再次按一下 [+新增] 按鈕以建立另一個連結服務。 
2. 在 [新增連結服務] 視窗中，選取 [Azure HDInsight]，然後按一下 [繼續]。 

    ![選取 Azure HDInsight](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. 在 [新增連結服務] 視窗中，執行下列步驟： 

    1. 輸入 **AzureHDInsightLinkedService** 作為 [名稱]。
    2. 確認在 [類型] 中選取 [隨選 HDInsight]。
    3. 選取 [AzureStorage1] 作為 [Azure 儲存體連結服務]。 您之前已建立此連結服務。 如果您使用不同的名稱，請在此欄位指定正確的名稱。 
    4. 選取 [spark] 作為 [叢集類型]。
    5. 輸入有權建立 HDInsight 叢集的 [服務主體識別碼]。 此服務主體必須是訂用帳戶之參與者角色的成員，或其中建立叢集之資源群組的成員。 如需詳細資料，請參閱[建立 Azure Active Directory 應用程式和服務主體](../azure-resource-manager/resource-group-create-service-principal-portal.md)。
    6. 輸入[服務主體金鑰]。 
    7. 選取您為 [資源群組] 建立資料處理站時使用的相同資源群組。 將在此資源群組中建立 Spark 叢集。 
    8. 展開 [OS 類型]。
    9. 輸入叢集 [使用者] 的 [名稱]。 
    10. 輸入使用者的 [密碼]。 
    11. 按一下 [檔案] 。 

        ![HDInsight 連結服務設定](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight 對您在其支援的每個 Azure 區域中可使用的核心總數有所限制。 對於隨選 HDInsight 連結服務，建立 HDInsight 叢集的位置與作為其主要儲存體之 Azure 儲存體的位置相同。 請確定您有足夠的核心配額，才能成功建立叢集。 如需詳細資訊，請參閱[使用 Hadoop、Spark 及 Kafka 等在 HDInsight 中設定叢集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。 

## <a name="create-a-pipeline"></a>建立管線

2. 按一下[+ (加號)] 按鈕，然後按一下功能表中的 [管線]。

    ![新增管線功能表](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. 在 [活動] 工具箱中展開 [HDInsight]，並將 [活動] 工具箱中的 [Spark] 活動拖放至管線設計工具介面。 

    ![拖放 Spark 活動](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. 在 [Spark] 活動視窗底部的屬性中，執行下列步驟： 

    1. 切換至 [HDI 叢集] 索引標籤。
    2. 選取您在上一個步驟中建立的 [AzureHDInsightLinkedService]。 
        
    ![指定 HDInsight 連結服務](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. 切換至 [指令碼/Jar] 索引標籤，執行下列步驟： 

    1. 選取 [AzureStorage1] 作為 [作業連結服務]。
    2. 按一下 [瀏覽儲存體]。 
    3. 瀏覽至 **adftutorial/spark/script 資料夾**，選取 **WordCount_Spark.py**，然後按一下[完成]。      

    ![指定 Spark 指令碼](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. 若要驗證管線，按一下工具列上的 [驗證] 按鈕。 按一下 [>>] (右箭頭) 按鈕以關閉驗證視窗。 
    
    ![驗證按鈕](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. 按一下 [發佈] 。 Data Factory 使用者介面會將實體 (連結的服務、管線) 發佈至 Azure Data Factory 服務。 
    
    ![發佈按鈕](./media/tutorial-transform-data-spark-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>觸發管線執行
按一下工具列上的 [觸發程序]，然後按一下 [立即觸發]。 

![立即觸發](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>監視管道執行

1. 切換至 [監視] 索引標籤。確認您看到管線執行。 建立 Spark 叢集需要約 20 分鐘。 

    ![監視管線回合](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. 定期按一下 [重新整理] 檢查管線執行的狀態。 

    ![管線執行狀態](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. 若要查看與此管線執行相關聯的活動執行，按一下 [動作] 資料行中的 [檢視活動執行] 動作。 您可以按一下頂端的 [管線] 連結，切換回管線執行檢視。

    ![活動執行檢視](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>驗證輸出
確認已在 adftutorial 容器的 spark/otuputfiles/wordcount 資料夾中建立輸出檔案。 

![驗證輸出](./media/tutorial-transform-data-spark-portal/verity-output.png)

這個檔案應該有輸入文字檔中的每個字組，以及字組在檔案中出現的次數。 例如︰ 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>後續步驟
此範例中的管線使用 Spark 活動和隨選 HDInsight 連結服務來轉換資料。 您已了解如何︰ 

> [!div class="checklist"]
> * 建立資料處理站。 
> * 建立具有 Spark 活動的管線
> * 觸發管線執行
> * 監視管道執行。

進入下一個教學課程，以了解如何在虛擬網路中的 Azure HDInsight 叢集上執行 Hive 指令碼，以轉換資料。 

> [!div class="nextstepaction"]
> [教學課程：在 Azure 虛擬網路中使用 Hive 來轉換資料](tutorial-transform-data-hive-virtual-network-portal.md)。





