---
title: Visual Studio Code 的 Azure HDInsight
description: 瞭解如何使用 Spark & Hive 工具 (Azure HDInsight) Visual Studio Code 來建立和提交查詢和腳本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 31f6c34089c1825eca21283b01eae181c8112216
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312173"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>使用 Spark & Hive 工具進行 Visual Studio Code

瞭解如何使用 Spark & Hive 工具來 Visual Studio Code 建立和提交 Apache Hive 批次作業、互動式 Hive 查詢, 以及適用于 Apache Spark 的 PySpark 腳本。 首先, 我們將說明如何在 Visual Studio Code 中安裝 Spark & Hive 工具, 然後逐步解說如何將作業提交至 Hive 和 Spark。  

Spark & Hive 工具可以安裝在 Visual Studio Code 支援的平臺上, 包括 Windows、Linux 和 macOS。 您可在下面找到不同平台的必要條件。


## <a name="prerequisites"></a>必要條件

需要有下列項目才能完成本文中的步驟：

- HDInsight 叢集。 若要建立叢集，請參閱[開始使用 HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。 或支援 Livy 端點的 Spark/Hive 叢集。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 Linux 和 macOS 才需要 Mono。
- [設定 Visual Studio Code 的 PySpark 互動式環境](set-up-pyspark-interactive-environment.md)。
- 名為 **HDexample** 的本機目錄。  本文使用 **C:\HD\HDexample**。

## <a name="install-spark--hive-tools"></a>安裝 Spark & Hive 工具

完成必要條件之後, 您可以為 Visual Studio Code 安裝 Spark & Hive 工具。  請完成下列步驟以安裝 Spark & Hive 工具:

1. 開啟 Visual Studio Code。

2. 從功能表列，瀏覽至 [檢視]   >  [延伸模組]  。

3. 在 [搜尋] 方塊中, 輸入**Spark & Hive**。

4. 從搜尋結果中選取 [ **Spark & Hive 工具**], 然後選取 [**安裝**]。  

   ![適用于 Visual Studio Code Python 安裝的 Spark & Hive](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 視需要**重載**。


## <a name="open-work-folder"></a>開啟工作資料夾

完成下列步驟，以開啟工作資料夾，並且在 Visual Studio Code 中建立檔案：

1. 從功能表列，瀏覽至 [檔案]   >  [開啟資料夾...]   >  **C:\HD\HDexample**，然後選取 [選取資料夾]  按鈕。 資料夾會出現在左側的 [總管]  檢視中。

2. 從 [總管]  檢視中，選取資料夾 **HDexample**，然後選取工作資料夾旁邊的 [新增檔案]  圖示。

   ![新增檔案](./media/hdinsight-for-vscode/new-file.png)

3. 將新`.hql`檔案命名為 (Hive 查詢) `.py`或 (Spark 腳本) 副檔名。  這個範例使用 **HelloWorld.hql**。

## <a name="set-the-azure-environment"></a>設定 Azure 環境

針對國家雲端使用者, 請遵循下列步驟來先設定 Azure 環境, 然後**使用 azure:登入**命令以登入 Azure。
   
1. 按一下 [ **File\Preferences\Settings**]。
2. 搜尋**Azure:形成**
3. 從清單中選取 [國家雲端]。

   ![設定預設的登入項目組態](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>連接到 Azure 帳戶

您必須先連線到您的 Azure 帳戶, 或連結叢集 (使用 Ambari 使用者名稱/密碼或加入網域的帳戶), 才可以將腳本提交至您 Visual Studio Code 的叢集。  完成下列步驟以連線至 Azure：

1. 從功能表列中, 流覽至 [**查看** > ] [**命令**選擇區 ... **], 然後輸入 Azure:登入**。

    ![適用于 Visual Studio Code 登入的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 遵循登入指示來登入 azure。 連線之後, 您的 Azure 帳戶名稱會顯示在 [Visual Studio Code] 視窗底部的狀態列上。  
  

## <a name="link-a-cluster"></a>連結叢集

### <a name="link-azure-hdinsight"></a>LinkAzure HDInsight

您可以使用 [Apache Ambari](https://ambari.apache.org/) 受控使用者名稱來連結正常的叢集，或可以使用網域使用者名稱 (例如：user1@contoso.com) 來連結企業安全性套件保護 Hadoop 叢集。

1. 從功能表列中, 流覽   > 至 流覽 **命令**選擇區 ... **, 然後輸入 Spark/Hive:** 連結叢集。

   ![連結叢集命令](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 選取連結的叢集類型 **Azure HDInsight**。

3. 輸入 HDInsight 叢集 URL。

4. 輸入 Ambari 使用者名稱，預設值是 **admin**。

5. 輸入 Ambari 密碼。

6. 選取叢集類型。

7. 設定叢集的顯示名稱 (選擇性)。

8. 檢閱 [輸出]  檢視以進行驗證。

   > [!NOTE]  
   > 如果叢集已登入 Azure 訂用帳戶並連結叢集，則會使用連結的使用者名稱和密碼。  


### <a name="link-generic-livy-endpoint"></a>Link泛型 Livy 端點

1. 從功能表列中, 流覽   > 至 流覽 **命令**選擇區 ... **, 然後輸入 Spark/Hive:** 連結叢集。

2. 選取連結的叢集類型 [泛型 Livy 端點]  。

3. 輸入泛型 Livy 端點，例如：http\://10.172.41.42:18080。

4. 選取授權類型 [基本]  或 [無]  。  如果是 [基本]  ，則：  
    &emsp;a. 輸入 Ambari 使用者名稱，預設值是 **admin**。  
    &emsp;b. 輸入 Ambari 密碼。

5. 檢閱 [輸出]  檢視以進行驗證。

## <a name="list-clusters"></a>列出叢集

1. 從功能表列中, 流覽   > 至 [流覽] [**命令**選擇區 ... **], 然後輸入 Spark/Hive:列出叢集**。

2. 選取所需的訂用帳戶。

3. 檢閱 [輸出]  檢視。  檢視會顯示您的 Azure 訂用帳戶下的連結叢集和所有叢集。

    ![設定預設叢集組態](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>設定預設叢集

1. 如果[先前](#open-work-folder)建立的 **HDexample** 資料夾已關閉，則重新開啟。  

2. 選取[先前](#open-work-folder)建立的檔案 **HelloWorld.hql**，它會在指令碼編輯器中開啟。

3. 以滑鼠右鍵按一下腳本編輯器, 然後選取 **[Spark/Hive:** 設定預設叢集]。  

4. [連接](#connect-to-azure-account)到您的 Azure 帳戶, 或連結叢集 (如果您尚未這麼做)。

5. 選取某個叢集來作為目前指令碼檔案的預設叢集。 工具會自動更新組態檔 **.VSCode\settings.json**。 

   ![設定預設叢集組態](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>提交互動式 Hive 查詢、Hive 批次指令碼

透過 Spark & Hive Tools for Visual Studio Code, 您可以將互動式 Hive 查詢和 Hive 批次腳本提交至您的叢集。

1. 如果[先前](#open-work-folder)建立的 **HDexample** 資料夾已關閉，則重新開啟。  

2. 選取[先前](#open-work-folder)建立的檔案 **HelloWorld.hql**，它會在指令碼編輯器中開啟。


3. 複製以下程式碼並貼到 Hive 檔案中，然後儲存該檔案。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [連接](#connect-to-azure-account)到您的 Azure 帳戶, 或連結叢集 (如果您尚未這麼做)。

5. 以滑鼠右鍵按一下腳本編輯器, 然後**選取 Hive:** 互動式 表示提交查詢, 或使用快速鍵**Ctrl + Alt + I**。選取**Hive:批**次以提交腳本, 或使用快速鍵**Ctrl + Alt + H**。  

6. 如果您還未指定預設的叢集，請選取叢集。 工具也可讓您使用快顯功能表來提交程式碼區塊，而非整個指令碼檔案。 在幾分鐘之後，查詢結果就會顯示在新的索引標籤中。

   ![互動式 Hive 結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - [結果]  面板：您可以將整個結果以 CSV、JSON 或 Excel 檔案的形式儲存到本機路徑，或只選取多行。

    - [訊息]  面板：當您選取 [行號]  時，它就會跳至執行中指令碼的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交互動式 PySpark 查詢

您可以遵循下列步驟來提交互動式 PySpark 查詢:

1. 如果[先前](#open-work-folder)建立的 **HDexample** 資料夾已關閉，則重新開啟。  

2. 緊接著[先前](#open-work-folder)的步驟建立新檔案 **HelloWorld.py**。

3. 複製以下程式碼並貼到指令碼檔案中：
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. [連接](#connect-to-azure-account)到您的 Azure 帳戶, 或連結叢集 (如果您尚未這麼做)。

5. 選擇所有程式碼, 並以滑鼠右鍵按一下腳本編輯器, **然後選取 [Spark:** PySpark 互動式] 以提交查詢，或使用快速鍵 **Ctrl + Alt + I**。

   ![pyspark 互動式操作功能表](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 如果您還未指定預設的叢集，請選取叢集。 幾分鐘後, **Python 互動式**結果會出現在新的索引標籤中。工具也可讓您使用快顯功能表來提交程式碼區塊，而非整個指令碼檔案。 

   ![pyspark 互動式 python 互動式視窗](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. 輸入 **"%% info"** , 然後按**Shift + enter**以查看作業資訊。 (選用)

   ![查看作業資訊](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. 此工具也支援**SPARK SQL**查詢。

   ![Pyspark 互動式視圖結果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   當您執行查詢時, 提交狀態會顯示在底部狀態列的左邊。 當狀態為 [PySpark 核心 (忙碌)]  時，請勿提交其他查詢。  

   > [!NOTE] 
   >
   > 在設定中取消選取 [**啟用 Python 延伸**模組] (預設設定為 [已核取]) 時, 提交的 pyspark 互動結果將使用舊的視窗。
   >
   > ![pyspark 互動式 python 延伸模組已停用](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批次作業

1. 如果[先前](#open-work-folder)建立的 **HDexample** 資料夾已關閉，則重新開啟。  

2. 緊接著[先前](#open-work-folder)的步驟建立新檔案 **BatchFile.py**。

3. 複製以下程式碼並貼到指令碼檔案中：

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [連接](#connect-to-azure-account)到您的 Azure 帳戶, 或連結叢集 (如果您尚未這麼做)。

5. 以滑鼠右鍵按一下腳本編輯器, 然後選取 **[Spark:** PySpark 批次]，或使用快速鍵 **Ctrl + Alt + H**。 

6. 選取一個叢集來提交 PySpark 作業。 

   ![提交 Python 作業結果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

在提交 Python 作業後，提交記錄會出現在 Visual Studio Code 的 [輸出]  視窗中。 此外也會顯示 **Spark UI URL** 和 **Yarn UI URL**。 您可以在網頁瀏覽器中開啟 URL 來追蹤作業狀態。

## <a name="apache-livy-configuration"></a>Apache Livy 設定

系統支援 [Apache Livy](https://livy.incubator.apache.org/) 設定，您可以在工作空間資料夾的 **.VSCode\settings.json** 中加以設定。 目前，Livy 設定僅支援 Python 指令碼。 如需詳細資料，請參閱 [Livy 讀我檔案](https://github.com/cloudera/livy/blob/master/README.rst )。

<a id="triggerlivyconf"></a>**如何觸發 Livy 設定**

方法 1  
1. 從功能表，瀏覽至 [檔案]   >  [喜好設定]   >  [設定]  。  
2. 在 [搜尋設定]  文字方塊中輸入 **HDInsight Job Sumission：Livy Conf**。  
3. 選取 [在 settings.json 中編輯]  以取得相關搜尋結果。

方法 2   
提交檔案，但請注意，.vscode 資料夾會自動新增至工作資料夾。 您可以按一下 **.vscode\settings.json** 以尋找 Livy 組態。

+ 專案設定：

    ![Livy 設定](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>針對 **driverMomory** 和 **executorMomry** 設定，設定附有單位的值，例如 1g 或 1024m。 

+ 所支援的 Livy 設定：   

    **POST /batches**   
    要求本文

    | name | description | type | 
    | :- | :- | :- | 
    | file | 要執行的應用程式所在的檔案 | 路徑 (必要) | 
    | proxyUser | 要在執行作業時模擬的使用者 | string | 
    | className | 應用程式 Java/Spark 主要類別 | string |
    | args | 應用程式的命令列引數 | 字串的清單 | 
    | jars | 要在此工作階段中使用的 jar | 字串清單 | 
    | pyFiles | 要在此工作階段中使用的 Python 檔案 | 字串清單 |
    | files | 要在此工作階段中使用的檔案 | 字串清單 |
    | driverMemory | 要用於驅動程式處理序的記憶體數量 | string |
    | driverCores | 要用於驅動程式處理序的核心數量 | ssNoversion |
    | executorMemory | 每一個執行程式處理序所要使用的記憶體數量 | string |
    | executorCores | 每個執行程式所要使用的核心數量 | ssNoversion |
    | numExecutors | 要為此工作階段啟動的執行程式數量 | ssNoversion |
    | archives | 要在此工作階段中使用的封存 | 字串清單 |
    | queue | 所提交到的 YARN 佇列名稱 | string |
    | name | 此工作階段的名稱 | string |
    | conf | Spark 組態屬性 | key=val 的對應 |

    回應本文   
    所建立的 Batch 物件。

    | name | description | type | 
    | :- | :- | :- | 
    | id | 工作階段識別碼 | ssNoversion | 
    | appId | 此工作階段的應用程式識別碼 |  字串 |
    | appInfo | 詳細的應用程式資訊 | key=val 的對應 |
    | log | 記錄行 | 字串的清單 |
    | state |   批次狀態 | string |

>[!NOTE]
>指派的 Livy 組態會在提交指令碼執行時顯示於輸出窗格中。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>從總管整合 Azure HDInsight

**Azure HDInsight** 已新增至 [總管] 檢視。 您可以直接透過 **Azure HDInsight** 瀏覽及管理您的叢集。

1. [連接](#connect-to-azure-account)到您的 Azure 帳戶, 或連結叢集 (如果您尚未這麼做)。

2. 從功能表列，瀏覽至 [檢視]   >  [總管]  。

3. 從左窗格中，展開 **AZURE HDINSIGHT**。  可用的訂用帳戶和叢集 (支援 Spark、Hadoop 和 HBase) 將會列出。 

   ![Azure HDInsight 訂用帳戶](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. 展開叢集以檢視 Hive 中繼資料資料庫和資料表結構描述。

   ![Azure HDInsight 叢集](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>預覽 Hive 資料表
您可以直接透過**Azure HDInsight** explorer, 在叢集中預覽 Hive 資料表。
1. [連線](#connect-to-azure-account)到 Azure 帳戶 (如果您尚未這樣做)。

2. 按一下最左邊資料行中的 [ **Azure** ] 圖示。

3. 從左窗格中，展開 **AZURE HDINSIGHT**。 將會列出可用的訂用帳戶和叢集。

4. 展開叢集以檢視 Hive 中繼資料資料庫和資料表結構描述。

5. 以滑鼠右鍵按一下 Hive 資料表, 例如 hivesampletable。 選取 [**預覽**]。 

   ![適用于 visual studio code preview hive 資料表的 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. [**預覽結果**] 視窗隨即開啟。

   ![適用于 visual studio code 的 Spark & Hive 預覽結果視窗](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **結果**面板

   您可以將整個結果以 CSV、JSON 或 Excel 檔案的形式儲存到本機路徑，或只選取多行。

- **訊息**面板
   1. 當資料表中的資料列數目大於100個數據列時, 訊息會顯示:**系統會針對 Hive 資料表顯示前 100**個數據列。
   2. 當資料表中的資料列數目小於或等於100資料列時, 訊息會顯示:**系統會針對 Hive 資料表顯示60資料列**。
   3. 當資料表中沒有任何內容時, 訊息會顯示:**針對 Hive 資料表顯示0個數據列**。

>[!NOTE]
>
>在 Linux 中, 安裝 xclip 以啟用複製資料表資料。
>
>![Linux 中 Visual Studio 程式碼的 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>其他功能

適用于 Visual Studio Code 的 Spark & Hive 支援下列功能:

- **IntelliSense 自動完成**。 關鍵字、方法、變數等的建議快顯視窗。 不同圖示代表不同類型的物件。

    ![適用于 Visual Studio Code IntelliSense 物件類型的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 錯誤標記**。 語言服務會對 Hive 指令碼的編輯錯誤加上底線。     
- **語法醒目顯示**。 語言服務會使用不同顏色來區別變數、關鍵字、資料類型、函式等等。 

    ![適用于 Visual Studio Code 語法重點的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>僅限讀取者角色

具有「**僅限**叢集**讀取**者」**角色**的使用者無法再將作業提交至 HDInsight 叢集, 也無法查看 Hive 資料庫。 請洽詢叢集系統管理員, 將您的角色升級至[Azure 入口網站](https://ms.portal.azure.com/)中的[ **HDInsight**  叢集**操作員**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) 。 如果您知道 Ambari 認證, 您可以依照下列指示手動連結叢集。

### <a name="browse-hdinsight-cluster"></a>流覽 HDInsight 叢集  

當您按一下 Azure HDInsight explorer 以擴充 HDInsight 叢集時, 如果您是叢集的僅讀取者角色, 系統會提示您連結叢集。 請遵循下列步驟, 透過 Ambari 認證連結到叢集。 

### <a name="submit-job-to-hdinsight-cluster"></a>將作業提交至 HDInsight 叢集

將作業提交至 HDInsight 叢集時, 如果您是叢集的僅讀取者角色, 系統會提示您連結叢集。 請遵循下列步驟, 透過 Ambari 認證連結到叢集。 

### <a name="link-to-cluster"></a>連結至叢集

1.  輸入 Ambari 使用者名稱 
2.  輸入 Ambari 使用者密碼。

   ![適用于 Visual Studio Code 使用者名稱的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![適用于 Visual Studio Code 密碼的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>您可以使用 Spark/Hive:列出叢集以檢查連結的叢集。
>
>![已連結 Visual Studio Code 讀取器的 Spark & Hive 工具](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>流覽 ADLS Gen2 帳戶

按一下 Azure HDInsight explorer 以展開 ADLS Gen2 帳戶時, 如果您的 Azure 帳戶沒有 Gen2 儲存體的存取權, 系統會提示您輸入儲存體**存取金鑰**。 一旦成功驗證存取金鑰, ADLS Gen2 帳戶就會自動展開。 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>使用 ADLS Gen2 將作業提交至 HDInsight 叢集

當您使用 ADLS Gen2 將作業提交至 HDInsight 叢集時, 如果您的 Azure 帳戶沒有 Gen2 儲存體的寫入權限, 系統將會提示您輸入儲存體**存取金鑰**。  成功驗證存取金鑰後, 作業就會成功提交。 

![適用于 Visual Studio Code AccessKey 的 Spark & Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>您可以從 Azure 入口網站取得儲存體帳戶的存取金鑰。 如需相關資訊, 請參閱[查看及複製存取金鑰](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys)。

## <a name="unlink-cluster"></a>取消連結叢集

1. 從功能表列中, 流覽   > 至 [流覽] [命令選擇區 **...** ] **, 然後輸入 Spark/Hive:Unlink a Cluster**。  

2. 選取要取消連結的叢集。  

3. 檢閱 [輸出]  檢視以進行驗證。  

## <a name="sign-out"></a>登出  

從功能表列中, 流覽   > 至 [流覽] [命令選擇區 **...** ] **, 然後輸入 Azure:** 登出。


## <a name="next-steps"></a>後續步驟
如需使用 Spark & Hive 進行 Visual Studio Code 的示範影片, 請參閱[適用于 Visual Studio Code 的 spark & hive](https://go.microsoft.com/fwlink/?linkid=858706)
