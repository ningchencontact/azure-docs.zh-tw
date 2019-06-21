---
title: Azure HDInsight 工具-使用適用於 Hive、 LLAP 或 PySpark 的 Visual Studio Code
description: 了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具來建立及提交查詢和指令碼。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: aadfae9a7b74986fd0ac8857669dd3ccaf62af1f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166200"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>使用適用於 Visual Studio Code 的 Azure HDInsight 工具

了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具，建立、提交適用於 Apache Spark 的 Apache Hive 批次作業、互動式 Hive 查詢和 PySpark 指令碼。 首先我們會說明如何在 Visual Studio Code 中安裝 HDInsight 工具，然後逐步解說如何將作業提交至 Hive 和 Spark。  

Azure HDInsight 工具可以安裝在 Visual Studio Code 支援的平台上，包括 Windows、Linux 和 macOS。 您可在下面找到不同平台的必要條件。


## <a name="prerequisites"></a>必要條件

需要有下列項目才能完成本文中的步驟：

- HDInsight 叢集。 若要建立叢集，請參閱[開始使用 HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 Linux 和 macOS 才需要 Mono。
- 適用於 Visual Studio Code 的 [Azure 帳戶延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。
- [設定 Visual Studio Code 的 PySpark 互動式環境](set-up-pyspark-interactive-environment.md)。
- 名為 **HDexample** 的本機目錄。  本文使用 **C:\HD\HDexample**。

## <a name="install-azure-hdinsight-tools"></a>安裝 Azure HDInsight 工具

完成必要條件之後，您就可以安裝適用於 Visual Studio Code 的 Azure HDInsight 工具。  請完成下列步驟來安裝 Azure HDInsight 工具：

1. 開啟 Visual Studio Code。

2. 從功能表列，瀏覽至 [檢視]   >  [延伸模組]  。

3. 在搜尋方塊中輸入 **HDInsight**。

4. 從搜尋結果中選取 [Azure HDInsight 工具]  ，然後選取 [安裝]  。  

   ![HDInsight for Visual Studio Code Python 安裝](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 安裝之後，選取 [重新載入]  來啟動 **Azure HDInsight 工具**延伸模組。


## <a name="open-hdinsight-work-folder"></a>開啟 HDInsight 工作資料夾

完成下列步驟，以開啟工作資料夾，並且在 Visual Studio Code 中建立檔案：

1. 從功能表列，瀏覽至 [檔案]   >  [開啟資料夾...]   >  **C:\HD\HDexample**，然後選取 [選取資料夾]  按鈕。 資料夾會出現在左側的 [總管]  檢視中。

2. 從 [總管]  檢視中，選取資料夾 **HDexample**，然後選取工作資料夾旁邊的 [新增檔案]  圖示。

   ![新增檔案](./media/hdinsight-for-vscode/new-file.png)

3. 使用新的檔案命名`.hql`(Hive 查詢) 或`.py`（Spark 指令碼） 副檔名。  這個範例使用 **HelloWorld.hql**。

## <a name="set-the-azure-environment"></a>設定 Azure 環境

1. [連接](#connect-to-azure-account)至您的 Azure 帳戶，或連結叢集，如果您還尚未這麼做。

2. 從功能表列中，瀏覽至 檢視   >  命令選擇區...  ，然後輸入 **HDInsight:** 設定 Azure 環境。

3. 選取環境作為預設登入項目。

4. 同時，工具已將您的預設登入項目儲存在 **.VSCode\settings.json** 中。 您也可以直接在這個組態檔內更新此登入項目。 

   ![設定預設的登入項目組態](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>連線到 Azure 帳戶

將指令碼從 Visual Studio Code 提交到 HDInsight 叢集之前，您需要先連線 Azure 帳戶或叢集 (使用 Ambari 使用者名稱/密碼或加入網域的帳戶)。  完成下列步驟以連線至 Azure：

1. 從功能表列中，瀏覽至 檢視   >  命令選擇區...  ，然後輸入 **HDInsight:** 登入。

    ![「適用於 Visual Studio Code 的 HDInsight 工具」登入](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 請遵循登入中的指示**輸出**窗格。
    + 對於 Azure 全域環境，**HDInsight：登入**命令將會在 HDInsight 總管中觸發**登入 Azure** 動作，反之亦然。

        ![Azure 的登入指示](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + 對於其他環境中，請遵循登入指示。

        ![其他環境的登入指示](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   連線之後，您的 Azure 帳戶名稱會顯示在 Visual Studio Code 視窗左下角的狀態列上。  
  

## <a name="link-a-cluster"></a>連結叢集

### <a name="link-azure-hdinsight"></a>連結：Azure HDInsight

您可以使用 [Apache Ambari](https://ambari.apache.org/) 受控使用者名稱來連結正常的叢集，或可以使用網域使用者名稱 (例如：user1@contoso.com) 來連結企業安全性套件保護 Hadoop 叢集。

1. 從功能表列中，瀏覽至 檢視   >  命令選擇區...  ，然後輸入 **HDInsight:** 連結叢集。

   ![連結叢集命令](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 選取連結的叢集類型 **Azure HDInsight**。

3. 輸入 HDInsight 叢集 URL。

4. 輸入 Ambari 使用者名稱，預設值是 **admin**。

5. 輸入 Ambari 密碼。

6. 選取叢集類型。

7. 檢閱 [輸出]  檢視以進行驗證。

   > [!NOTE]  
   > 如果叢集已登入 Azure 訂用帳戶並連結叢集，則會使用連結的使用者名稱和密碼。  



### <a name="link-generic-livy-endpoint"></a>連結：泛型 Livy 端點

1. 從功能表列中，瀏覽至 檢視   >  命令選擇區...  ，然後輸入 **HDInsight:** 連結叢集。

2. 選取連結的叢集類型 [泛型 Livy 端點]  。

3. 輸入泛型 Livy 端點，例如：http\://10.172.41.42:18080。

4. 選取授權類型 [基本]  或 [無]  。  如果是 [基本]  ，則：  
    &emsp;a. 輸入 Ambari 使用者名稱，預設值是 **admin**。  
    &emsp;b. 輸入 Ambari 密碼。

5. 檢閱 [輸出]  檢視以進行驗證。

## <a name="list-hdinsight-clusters"></a>列出 HDInsight 叢集

1. 從功能表列中，瀏覽至 [檢視]   >  [命令選擇區...]  ，然後輸入 **HDInsight:列出叢集**。

2. 選取所需的訂用帳戶。

3. 檢閱 [輸出]  檢視。  檢視會顯示您的 Azure 訂用帳戶下的連結叢集和所有叢集。

    ![設定預設叢集組態](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>設定預設叢集

1. 如果[先前](#open-hdinsight-work-folder)建立的 **HDexample** 資料夾已關閉，則重新開啟。  

2. 選取[先前](#open-hdinsight-work-folder)建立的檔案 **HelloWorld.hql**，它會在指令碼編輯器中開啟。

3. 在指令碼編輯器上按一下滑鼠右鍵，然後選取 [HDInsight:  設定預設叢集]。  

4. [連接](#connect-to-azure-account)至您的 Azure 帳戶，或連結叢集，如果您還尚未這麼做。

5. 選取某個叢集來作為目前指令碼檔案的預設叢集。 工具會自動更新組態檔 **.VSCode\settings.json**。 

   ![設定預設叢集組態](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>提交互動式 Hive 查詢、Hive 批次指令碼

透過適用於 Visual Studio Code 的 HDInsight Tools，您可以將互動式 Hive 查詢、Hive 批次指令碼提交到 HDInsight 叢集。

1. 如果[先前](#open-hdinsight-work-folder)建立的 **HDexample** 資料夾已關閉，則重新開啟。  

2. 選取[先前](#open-hdinsight-work-folder)建立的檔案 **HelloWorld.hql**，它會在指令碼編輯器中開啟。


3. 複製以下程式碼並貼到 Hive 檔案中，然後儲存該檔案。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [連接](#connect-to-azure-account)至您的 Azure 帳戶，或連結叢集，如果您還尚未這麼做。

5. 在指令碼編輯器上按一下滑鼠右鍵，選取 [HDInsight：  Hive 互動式] 以提交查詢，或使用快速鍵 **Ctrl + Alt + I**。選取 [HDInsight：  Hive 批次] 以提交指令碼，或使用快速鍵 **Ctrl + Alt + H**。  

6. 如果您還未指定預設的叢集，請選取叢集。 工具也可讓您使用快顯功能表來提交程式碼區塊，而非整個指令碼檔案。 在幾分鐘之後，查詢結果就會顯示在新的索引標籤中。

   ![互動式 Hive 結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - [結果]  面板：您可以將整個結果以 CSV、JSON 或 Excel 檔案的形式儲存到本機路徑，或只選取多行。

    - [訊息]  面板：當您選取 [行號]  時，它就會跳至執行中指令碼的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交互動式 PySpark 查詢

您可以將互動式 PySpark 查詢提交藉由遵循下列步驟：

1. 如果[先前](#open-hdinsight-work-folder)建立的 **HDexample** 資料夾已關閉，則重新開啟。  

2. 緊接著[先前](#open-hdinsight-work-folder)的步驟建立新檔案 **HelloWorld.py**。

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

4. [連接](#connect-to-azure-account)至您的 Azure 帳戶，或連結叢集，如果您還尚未這麼做。

5. 選擇 [所有程式碼，並以滑鼠右鍵按一下指令碼編輯器中，選取**HDInsight:** PySpark 互動式] 以提交查詢，或使用快速鍵 **Ctrl + Alt + I**。

   ![以滑鼠右鍵按一下 pyspark 互動式](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 如果您還未指定預設的叢集，請選取叢集。 幾分鐘之後, **Python Interactive 結果**出現在新的索引標籤。工具也可讓您使用快顯功能表來提交程式碼區塊，而非整個指令碼檔案。 

   ![pyspark 互動式 python 互動式視窗](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Enter **"%%資訊 」** ，然後按**Shift + Enter**若要檢視作業資訊。 (選用)

   ![檢視作業資訊](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. 此工具也支援**Spark SQL**查詢。

   ![Pyspark 互動式檢視結果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   當您執行查詢時，提交狀態會出現在底部狀態列的左邊。 當狀態為 [PySpark 核心 (忙碌)]  時，請勿提交其他查詢。  

   > [!NOTE] 
   >
   > 當**Python 延伸模組啟用**未在設定 （預設為核取） 中，提交的 pyspark 互動結果將會使用舊的視窗。
   >
   > ![停用的 pyspark 互動式 python 延伸模組](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批次作業

1. 如果[先前](#open-hdinsight-work-folder)建立的 **HDexample** 資料夾已關閉，則重新開啟。  

2. 緊接著[先前](#open-hdinsight-work-folder)的步驟建立新檔案 **BatchFile.py**。

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

4. [連接](#connect-to-azure-account)至您的 Azure 帳戶，或連結叢集，如果您還尚未這麼做。

5. 在指令碼編輯器上按一下滑鼠右鍵，然後從快顯功能表選取 [HDInsight：  PySpark 批次]，或使用快速鍵 **Ctrl + Alt + H**。 

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
    | proxyUser | 要在執行作業時模擬的使用者 | 字串 | 
    | className | 應用程式 Java/Spark 主要類別 | 字串 |
    | args | 應用程式的命令列引數 | 字串的清單 | 
    | jars | 要在此工作階段中使用的 jar | 字串清單 | 
    | pyFiles | 要在此工作階段中使用的 Python 檔案 | 字串清單 |
    | files | 要在此工作階段中使用的檔案 | 字串清單 |
    | driverMemory | 要用於驅動程式處理序的記憶體數量 | 字串 |
    | driverCores | 要用於驅動程式處理序的核心數量 | int |
    | executorMemory | 每一個執行程式處理序所要使用的記憶體數量 | string |
    | executorCores | 每個執行程式所要使用的核心數量 | int |
    | numExecutors | 要為此工作階段啟動的執行程式數量 | int |
    | archives | 要在此工作階段中使用的封存 | 字串清單 |
    | queue | 所提交到的 YARN 佇列名稱 | 字串 |
    | name | 此工作階段的名稱 | 字串 |
    | conf | Spark 組態屬性 | key=val 的對應 |

    回應本文   
    所建立的 Batch 物件。

    | name | description | type | 
    | :- | :- | :- | 
    | id | 工作階段識別碼 | int | 
    | appId | 此工作階段的應用程式識別碼 |  String |
    | appInfo | 詳細的應用程式資訊 | key=val 的對應 |
    | log | 記錄行 | 字串的清單 |
    | state |   批次狀態 | string |

>[!NOTE]
>指派的 Livy 組態會在提交指令碼執行時顯示於輸出窗格中。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>從總管整合 Azure HDInsight

**Azure HDInsight** 已新增至 [總管] 檢視。 您可以直接透過 **Azure HDInsight** 瀏覽及管理您的叢集。

1. [連接](#connect-to-azure-account)至您的 Azure 帳戶，或連結叢集，如果您還尚未這麼做。

2. 從功能表列，瀏覽至 [檢視]   >  [總管]  。

3. 從左窗格中，展開 **AZURE HDINSIGHT**。  可用的訂用帳戶和叢集 (支援 Spark、Hadoop 和 HBase) 將會列出。 

   ![Azure HDInsight 訂用帳戶](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. 展開叢集以檢視 Hive 中繼資料資料庫和資料表結構描述。

   ![Azure HDInsight 叢集](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>預覽的 Hive 資料表
您可以預覽中直接透過您叢集的 Hive 資料表**Azure HDInsight**總管。
1. [連線](#connect-to-azure-account)到 Azure 帳戶 (如果您尚未這樣做)。

2. 按一下  **Azure**從最左邊的資料行的圖示。

3. 從左窗格中，展開 AZURE HDINSIGHT。 會列出可用的訂用帳戶和叢集。

4. 展開叢集以檢視 Hive 中繼資料資料庫和資料表結構描述。

5. 以滑鼠右鍵按一下 Hive 資料表，例如 hivesampletable。 選取 **預覽**。 

   ![HDInsight for vscode 預覽的 hive 資料表](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **預覽結果**將會開啟視窗。

   ![HDInsight for vscode 預覽結果視窗](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **結果**面板

   您可以將整個結果以 CSV、JSON 或 Excel 檔案的形式儲存到本機路徑，或只選取多行。

- **訊息**面板
   1. 資料表中的資料列數目超過 100 個資料列時，訊息就會顯示：**Hive 資料表會顯示前 100 個資料列**。
   2. 當資料表中的資料列數目小於或等於 100 個資料列時，便會顯示訊息：**60 的資料列會顯示為 Hive 資料表**。
   3. 當資料表中沒有任何內容時，訊息就會顯示：**Hive 資料表會顯示 0 列**。

>[!NOTE]
>
>在 Linux 中，安裝 xclip 啟用複製資料表資料。
>
>![在 linux 中的 vscode 的 HDInsight](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>其他功能

HDInsight for Visual Studio Code 支援下列功能︰

- **IntelliSense 自動完成**。 關鍵字、方法、變數等的建議快顯視窗。 不同圖示代表不同類型的物件。

    ![「適用於 Visual Studio Code 的 HDInsight 工具」IntelliSense 物件類型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 錯誤標記**。 語言服務會對 Hive 指令碼的編輯錯誤加上底線。     
- **語法醒目顯示**。 語言服務會使用不同顏色來區別變數、關鍵字、資料類型、函式等等。 

    ![「適用於 Visual Studio Code 的 HDInsight 工具」語法醒目顯示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>讀取器的唯一角色

與叢集使用者**讀者** **只** **角色**能不會再將工作提交至 HDInsight 叢集也不能檢視 Hive 資料庫。 您需要連絡叢集管理員以升級您的角色，才能[ **HDInsight** **叢集** **運算子**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)中[Azure 入口網站](https://ms.portal.azure.com/)。 如果您知道 Ambari 認證時，您可以手動連結遵循下列指示的叢集。

### <a name="browse-hdinsight-cluster"></a>瀏覽 HDInsight 叢集  

當按一下 Azure HDInsight 總管 中，展開 HDInsight 叢集，您將提示您連結的叢集，如果您是讀取器的唯一角色叢集。 請遵循下列步驟來連結至叢集透過 Ambari 認證。 

### <a name="submit-job-to-hdinsight-cluster"></a>將作業提交至 HDInsight 叢集

當提交至 HDInsight 叢集的作業，您將提示您連結的叢集，如果您是讀取器的唯一角色叢集。 請遵循下列步驟來連結至叢集透過 Ambari 認證。 

### <a name="link-to-cluster"></a>連結至叢集

1.  輸入 Ambari 使用者名稱 
2.  輸入 Ambari 使用者密碼。

   ![HDInsight Tools for Visual Studio 程式碼的使用者名稱](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![HDInsight Tools for Visual Studio 程式碼的密碼](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>您可以使用 HDInsight:列出叢集，若要檢查連結的叢集。
>
>![HDInsight Tools for Visual Studio 程式碼讀取器連結](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-reader-linked.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake 儲存體 Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>瀏覽 ADLS Gen2 帳戶

時按一下 Azure HDInsight 總管 中，展開 ADLS Gen2 帳戶，將會提示您輸入儲存體**便捷鍵**如果您的 Azure 帳戶不具有存取權的 Gen2 儲存體。 ADLS Gen2 帳戶將會自動展開後的存取金鑰驗證成功。 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>將作業提交至 HDInsight 叢集與 ADLS Gen2

當提交至 HDInsight 叢集與 ADLS Gen2 的作業，您將會提示您輸入儲存體**便捷鍵**如果您的 Azure 帳戶有沒有寫入存取權的 Gen2 儲存體。  已成功驗證的存取金鑰之後，就將已成功提交作業。 

![適用於 Visual Studio 程式碼 AccessKey 的 HDInsight 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>您可以從 Azure 入口網站取得儲存體帳戶的存取金鑰。 如需資訊，請參閱[檢視及複製存取金鑰](https://docs.microsoft.com/azure/storage/common/storage-account-manage#view-and-copy-access-keys)。

## <a name="unlink-cluster"></a>取消連結叢集

1. 從功能表列中，瀏覽至 [檢視]   >  [命令選擇區...]  ，然後輸入 **HDInsight:Unlink a Cluster**。  

2. 選取要取消連結的叢集。  

3. 檢閱 [輸出]  檢視以進行驗證。  

## <a name="sign-out"></a>登出  

從功能表列中，瀏覽至 [檢視]   >  [命令選擇區...]  ，然後輸入 **HDInsight:Logout**。  在右側底部會有快顯，指出**登入成功！** 。


## <a name="next-steps"></a>後續步驟
如需使用 HDInsight for Visual Studio Code 的示範影片，請參閱 [HDInsight for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)
