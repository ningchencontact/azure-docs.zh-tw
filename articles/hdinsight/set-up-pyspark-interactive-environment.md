---
title: Azure HDInsight 工具 - 設定 Visual Studio Code 的 PySpark 互動式環境
description: 了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具來建立及提交查詢和指令碼。
keywords: VScode,Azure HDInsight 工具,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,互動式 Hive,互動式查詢
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: 45ba049717f2b9874bbac8d6493e13c2afc4b8f2
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910643"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>設定 Visual Studio Code 的 PySpark 互動式環境

下列步驟說明如何在 VS Code 中設定 PySpark 互動式環境。

我們會使用 **python/pip** 命令在您的首頁路徑中建置虛擬環境。 如果想要使用其他版本，您需要手動變更 **python/pip** 命令的預設版本。 請參閱[更新替代項目](https://linux.die.net/man/8/update-alternatives)了解更多詳細資料。

1. 安裝 [Python](https://www.python.org/downloads/) 和 [pip](https://pip.pypa.io/en/stable/installing/)。
   
   + 從 [https://www.python.org/downloads/](https://www.python.org/downloads/) 安裝 Python。
   + 從 [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) 安裝 pip。 (如果未在安裝 Python 時安裝)
   + 使用下列命令驗證是否已成功安裝 Python 和 pip。 (選用)
 
        ![Python pip 版本](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

    > [!NOTE]
    > 建議您以手動方式安裝 Python，不要使用 MacOS 預設版本。


2. 執行下列命令來安裝 **virtualenv**。
   
   ```
   pip install virtualenv
   ```

3. 僅限 Linux，如果出現錯誤訊息，請執行下列命令安裝必要套件。
   
    ![Python pip 版本](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. 重新啟動 VS Code，然後返回執行 [HDInsight: PySpark 互動式] 的指令碼編輯器。

## <a name="next-steps"></a>後續步驟

### <a name="demo"></a>示範
* HDInsight for VS Code：[影片](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)
* [使用 Azure Toolkit for IntelliJ 來建立和提交 Apache Spark Scala 應用程式](spark/apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 透過 SSH 遠端偵錯 Apache Spark 應用程式](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [使用 Azure Toolkit for IntelliJ 透過 VPN 遠端偵錯 Apache Spark 應用程式](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 Apache Spark 應用程式](spark/apache-spark-eclipse-tool-plugin.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](spark/apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](spark/apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Apache Hive 資料視覺化](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢](hdinsight-connect-hive-zeppelin.md)
