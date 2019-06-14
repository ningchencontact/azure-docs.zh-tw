---
title: 指令碼動作 - 在 Azure HDInsight 上搭配 Jupyter 安裝 Python 套件
description: 說明如何使用指令碼動作以設定讓 HDInsight Spark 叢集隨附之 Jupyter Notebook 使用外部 Python 套件的逐步指示。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: c07326cc3a4334f1873eef2dc23da05156a93577
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574657"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>使用指令碼動作在 HDInsight Linux 上的 Apache Spark 叢集中安裝 Jupyter Notebook 的外部 Python 封裝
> [!div class="op_single_selector"]
> * [使用資料格魔術](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用指令碼動作](apache-spark-python-package-installation.md)

了解如何使用指令碼動作來設定[Apache Spark](https://spark.apache.org/)上使用外部、 社群提供的 HDInsight 叢集**python**套件不在叢集中包含的立即可用。

> [!NOTE]  
> 您也可以藉由使用 `%%configure` 魔術，設定 Jupyter Notebook 來使用外部的封裝。 如需指示，請參閱[在 HDInsight 上的 Apache Spark 叢集中搭配 Jupyter Notebook 使用外部封裝](apache-spark-jupyter-notebook-use-external-packages.md)。

您可以搜尋[套件索引](https://pypi.python.org/pypi)，以取得可用的套件完整清單。 您也可以從其他來源取得可用套件清單。 例如，您可以安裝經由 [conda-forge](https://conda-forge.org/feedstocks/) 提供的套件。

在本文中，您將學習如何在叢集上使用指令碼動作安裝 [TensorFlow](https://www.tensorflow.org/) 套件，並透過 Jupyter Notebook 來使用它作為範例。

## <a name="prerequisites"></a>必要條件
您必須滿足以下條件：

* Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 叢集。 如需指示，請參閱[在 Azure HDInsight 中建立 Apache Spark 叢集](apache-spark-jupyter-spark-sql.md)。

   > [!NOTE]  
   > 如果您在 HDInsight Linux 上還沒有 Spark 叢集，您可以在叢集建立期間執行指令碼動作。 瀏覽有關於[如何使用自訂指令碼動作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)的文件。
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支援在 HDInsight 叢集上使用開放原始碼軟體

Microsoft Azure HDInsight 服務會使用以 Apache Hadoop 為中心的開放原始碼技術生態系統。 Microsoft Azure 可為開放原始碼技術提供一般層級的支援。 如需詳細資訊，請參閱 [Azure 支援常見問題集網站](https://azure.microsoft.com/support/faq/)中的**支援範圍**一節。 HDInsight 服務針對內建元件提供額外等級的支援。

HDInsight 服務中有兩種類型的開放原始碼元件可用：

* **內建元件** - 這些元件預先安裝在 HDInsight 叢集上，並且提供叢集的核心功能。 例如，Apache Hadoop YARN ResourceManager、Apache Hive 查詢語言 (HiveQL) 及 Mahout 程式庫都屬於這個類別。 叢集元件的完整清單可於 [HDInsight 所提供 Apache Hadoop 叢集版本的新功能](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)中取得。
* **自訂元件** - 身為叢集使用者的您可以安裝社群中可用或是您建立的任何元件，或者在工作負載中使用。

> [!IMPORTANT]   
> 對隨 HDInsight 叢集提供的元件會有完整支援。 Microsoft 支援服務可協助隔離和解決這些元件的相關問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 Microsoft 支援服務可能可以解決問題，也可能要求您利用可用的開放原始碼技術管道，找到該技術的深度專業知識。 例如，有許多社群網站可供使用，例如：[MSDN 的 HDInsight 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)、[https://stackoverflow.com](https://stackoverflow.com)。 此外，Apache 專案在 [https://apache.org](https://apache.org) 上也有專案網站，例如：[Hadoop](https://hadoop.apache.org/)。


## <a name="use-external-packages-with-jupyter-notebooks"></a>搭配 Jupyter Notebook 使用外部套件

1. 從[Azure 入口網站](https://portal.azure.com/)，瀏覽至您的叢集。  

2. 您選取叢集後，從左窗格中，下方**設定**，選取**指令碼動作**。

3. 選取  **+ 送出新**。

4. 輸入下列值**送出指令碼動作**視窗：  


    |參數 | 值 |
    |---|---|
    |指令碼類型 | 從下拉式清單中選取 [- 自訂]  。|
    |Name |在文字方塊中輸入 `tensorflow`。|
    |Bash 指令碼 URI |在文字方塊中輸入 `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh`。 |
    |節點類型 | 選取  **Head**，並**工作者**核取方塊。 |

    `tensorflowinstall.sh` 包含下列命令：

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. 選取 [建立]  。  瀏覽有關於[如何使用自訂指令碼動作](../hdinsight-hadoop-customize-cluster-linux.md)的文件。

6. 等候指令碼完成。  **指令碼動作**會在狀態窗格**目前的叢集作業完成後，就可以提交新的指令碼動作**指令碼執行時。  您可以從 Ambari UI 中檢視進度列**背景作業**視窗。

7. 開啟 PySpark Jupyter notebook。  請參閱[建立 Jupyter notebook 在 Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight)的步驟。

    ![建立新的 Jupyter Notebook](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "建立新的 Jupyter Notebook")

8. 現在，您將 `import tensorflow` 並執行 hello world 範例。 輸入下列程式碼：

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    結果如下所示：
    
    ![TensorFlow 程式碼執行](./media/apache-spark-python-package-installation/execution.png "執行 TensorFlow 程式碼")

> [!NOTE]  
> 叢集中有兩個 Python 安裝。 Spark 會使用位於 `/usr/bin/anaconda/bin` 的 Anaconda Python 安裝，並且會預設為 Python 2.7 環境。 若要使用 Python 3.x 並安裝 PySpark3 核心中的套件，請使用該環境的 `conda` 可執行檔路徑，並使用 `-n` 參數來指定環境。 例如，`/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` 命令會使用 `conda-forge` 通道將 `ggplot` 套件安裝至 Python 3.5 環境。

## <a name="seealso"></a>另請參閱
* [概觀：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>案例
* [Apache Spark 和 BI：在 HDInsight 中搭配使用 Spark 和 BI 工具執行互動式資料分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和 Machine Learning：使用 HDInsight 中的 Spark，利用 HVAC 資料來分析建築物溫度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和 Machine Learning：使用 HDInsight 中的 Spark 來預測食品檢查結果](apache-spark-machine-learning-mllib-ipython.md)
* [在 HDInsight 中使用 Apache Spark 進行網站記錄分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>建立及執行應用程式
* [使用 Scala 建立獨立應用程式](apache-spark-create-standalone-application.md)
* [利用 Apache Livy 在 Apache Spark 叢集上遠端執行作業](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [在 HDInsight 上的 Apache Spark 叢集中搭配 Jupyter Notebook 使用外部套件](apache-spark-jupyter-notebook-use-external-packages.md)
* [使用 IntelliJ IDEA 的 HDInsight Tools 外掛程式來建立和提交 Spark Scala 應用程式](apache-spark-intellij-tool-plugin.md)
* [使用適用於 IntelliJ IDEA 的 HDInsight 工具外掛程式遠端偵錯 Apache Spark 應用程式](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](apache-spark-zeppelin-notebook.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](apache-spark-jupyter-notebook-kernels.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理資源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [追蹤和偵錯在 HDInsight 中的 Apache Spark 叢集上執行的作業](apache-spark-job-debugging.md)
