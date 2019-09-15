---
title: 在本機安裝 Jupyter 並連線到 Azure HDInsight 中的 Spark
description: 了解如何在電腦本機安裝 Jupyter Notebook，並連線到 Apache Spark 叢集。
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: 6ce3ff8e00bc92911a7405de1bb0bb7286fb5c15
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993772"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>在電腦上安裝 Jupyter Notebook，並連線到 HDInsight 上的 Apache Spark

在這篇文章中，您會了解如何搭配含有 Spark magic 的自訂 PySpark (適用於 Python) 和 Apache Spark (適用於 Scala) 核心來安裝 Jupyter 筆記本，然後將筆記本連接到 HDInsight 叢集。 在您的本機電腦上安裝 Jupyter 可以有數種原因，而且也會面臨數種挑戰。 如需詳細資訊，請參閱本文章結尾的[為什麼我應該在我的電腦上安裝 Jupyter](#why-should-i-install-jupyter-on-my-computer) 一節。

安裝 Jupyter 和連線至 HDInsight 上的 Apache Spark 時，有四個主要步驟。

* 設定 Spark 叢集。
* 安裝 Jupyter Notebook。
* 安裝含有 Spark magic 的 PySpark 和 Spark 核心。
* 設定 Spark magic 以存取 HDInsight 上的 Spark 叢集。

如需有關可供 HDInsight 叢集之相關 Jupyter Notebook 使用的自訂核心和 Spark magic 的詳細資訊，請參閱 [HDInsight 上的 Apache Spark Linux 叢集可供 Jupyter Notebook 使用的核心](apache-spark-jupyter-notebook-kernels.md)。

## <a name="prerequisites"></a>必要條件

此處所列的必要條件不是針對安裝 Jupyter。 這些是用來在安裝 Notebook 之後將 Jupyter Notebook 連接到 HDInsight 叢集。

* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

## <a name="install-jupyter-notebook-on-your-computer"></a>在電腦上安裝 Jupyter Notebook

您必須先安裝 Python，才能安裝 Jupyter Notebook。 [Anaconda 散發](https://www.anaconda.com/download/)套件將會同時安裝、Python 和 Jupyter Notebook。

下載您平台適用的 [Anaconda 安裝程式](https://www.anaconda.com/download/) ，然後執行安裝程式。 執行安裝精靈時，請確定您選取將 Anaconda 新增至 PATH 變數的選項。  另請參閱[使用 Anaconda 安裝 Jupyter](https://jupyter.readthedocs.io/en/latest/install.html)。

## <a name="install-spark-magic"></a>安裝 Spark 魔術

1. 輸入下列其中一個命令，以安裝 Spark 魔術。 另請參閱[sparkmagic 檔](https://github.com/jupyter-incubator/sparkmagic#installation)。

    |叢集版本 | 安裝命令 |
    |---|---|
    |3\.6 和3.5 版 |`pip install sparkmagic==0.12.7`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. 執行`ipywidgets`下列命令，確定已正確安裝：

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>安裝 PySpark 和 Spark 核心

1. 輸入下列`sparkmagic`命令來識別安裝位置：

    ```cmd
    pip show sparkmagic
    ```

    然後將您的工作目錄變更為使用上述命令所識別的位置。

1. 從您的新工作目錄中，輸入下列一個或多個命令，以安裝所需的核心：

    |核心 | 命令 |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. 選擇性。 輸入下列命令以啟用伺服器擴充功能：

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>設定 Spark magic 以連線到 HDInsight Spark 叢集

在本節中，您會設定您先前安裝的 Spark 神奇，以連線至 Apache Spark 叢集。

1. 使用下列命令啟動 Python shell：

    ```cmd
    python
    ```

2. Jupyter 組態資訊通常儲存在使用者主目錄中。 輸入下列命令以識別主目錄，並建立名為**sparkmagic**的資料夾。  將會輸出完整路徑。

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. 在資料夾`.sparkmagic`內，建立名為**config.xml**的檔案，並在其中新增下列 json 程式碼片段。  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. 對檔案進行下列編輯：

    |範本值 | 新增值 |
    |---|---|
    |{USERNAME}|叢集登入，預設`admin`值為。|
    |CLUSTERDNSNAME|叢集名稱|
    |{BASE64ENCODEDPASSWORD}|以 base64 編碼的密碼，適用于您的實際密碼。  您可以在[https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/)產生 base64 密碼。|
    |`"livy_server_heartbeat_timeout_seconds": 60`|如果使用`sparkmagic 0.12.7` （叢集3.5 和3.6 版），請保留。  如果使用`sparkmagic 0.2.3` （叢集3.4），請將取代`"should_heartbeat": true`為。|

    您可以在[範例 config.xml](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)中看到完整的範例檔案。

   > [!TIP]  
   > 傳送活動訊號可確保不會流失工作階段。 當電腦進入睡眠或已關機時，則不會傳送活動訊號，導致工作階段被清除。 若為叢集 3.4 版，如果想要停用此行為，您可以從 Ambari UI 將 Livy 組態 `livy.server.interactive.heartbeat.timeout` 設定為 `0`。 若為叢集 3.5 版，如果您未設定上述的 3.5 組態，則不會刪除工作階段。

5. 啟動 Jupyter。 從命令提示字元使用下列命令。

    ```cmd
    jupyter notebook
    ```

6. 確認您可以使用核心所提供的 Spark 魔術。 請執行下列步驟：

    a. 建立新的 Notebook。 從右上角選取 [**新增**]。 您應該會看到預設的核心**Python 2**或**Python 3** ，以及您安裝的核心。 實際的值可能會根據您的安裝選項而有所不同。  選取 [ **PySpark**]。

    ![Jupyter Notebook 中的核心](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jupyter Notebook 中的核心")

    > [!IMPORTANT]  
    > 選取 [**新增**] 之後，請檢查您的 shell 是否有任何錯誤。  如果您看到錯誤`TypeError: __init__() got an unexpected keyword argument 'io_loop'` ，您可能會遇到某些龍捲風版本的已知問題。  若是如此，請停止核心，然後使用下列命令將您的龍捲風安裝降級`pip install tornado==4.5.3`：。

    b. 執行下列程式碼片段。

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    如果您可以順利擷取輸出，即表示已測試您對 HDInsight 叢集的連線。

    如果您想要更新筆記本設定以連線到不同的叢集，請使用一組新的值來更新 config.xml，如上面的步驟3所示。

## <a name="why-should-i-install-jupyter-on-my-computer"></a>為什麼我應該在我的電腦上安裝 Jupyter？

您想要在您的電腦上安裝 Jupyter，然後將其連接至 HDInsight 上的 Apache Spark 叢集，可能有數種原因。

* 雖然 Azure HDInsight 的 Spark 叢集上已經有 Jupyter Notebook 可用，但在電腦上安裝 Jupyter 可讓您選擇在本機建立 Notebook、針對正在執行的叢集測試您的應用程式，然後將 Notebook 上傳到叢集。 若要將 Notebook 上傳到叢集，您可以使用在叢集上執行的 Jupyter Notebook 來上傳它們，或將它們儲存到與叢集關聯之儲存體帳戶的 [/HdiNotebooks] 資料夾中。 如需有關 Notebook 在叢集上的儲存方式詳細資訊，請參閱 [Jupyter Notebook 會儲存在哪裡](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)？
* 使用本機可用的 Notebook，您可以根據您的應用程式需求，連接至不同的 Spark 叢集。
* 您可以使用 GitHub 實作來源控制系統，並且具有 Notebook 的版本控制。 您也可以有共同作業環境，其中多個使用者可以使用相同的 Notebook。
* 您甚至不需要啟用叢集，即可在本機使用 Notebook。 您只需要叢集針對 Notebook 進行測試，不必以手動方式管理您的 Notebook 或開發環境。
* 設定自己的本機開發環境比設定叢集的 Jupyter 安裝更容易。  您可以利用您在本機安裝的所有軟體，而不需要設定一或多個遠端叢集。

> [!WARNING]  
> 將 Jupyter 安裝在本機電腦上，多個使用者可以同時在相同的 Spark 叢集上執行相同的 Notebook。 在這種情況下，會建立多個 Livy 工作階段。 如果您遇到問題，而且想要偵錯，則追蹤哪個 Livy 工作階段屬於哪個使用者會是複雜的工作。  

## <a name="next-steps"></a>後續步驟

* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [Apache Spark 搭配 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和 Machine Learning：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)