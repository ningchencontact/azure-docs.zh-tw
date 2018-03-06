---
title: "開始使用 HDInsight 中的 R 伺服器 - Azure | Microsoft Docs"
description: "了解如何在包含 R 伺服器的 HDInsight 叢集上建立 Apache Spark，並在叢集上提交 R 指令碼。"
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.openlocfilehash: aa7f2e6f44036738756391ecaa265c57c093c42c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>開始在 HDInsight 中使用 R 伺服器

Azure HDInsight 包含要整合至 HDInsight 叢集的 R 伺服器選項。 此選項可讓 R 指令碼使用 Spark 和 MapReduce 來執行分散式計算。 在本文中，您將了解如何在 HDInsight 叢集上建立 R 伺服器。 接著，您將了解如何執行 R 指令碼，讓其示範如何使用 Spark 進行分散式 R 計算。


## <a name="prerequisites"></a>先決條件

* **Azure 訂用帳戶**：開始進行本教學課程之前，您必須擁有 Azure 訂用帳戶。 如需詳細資訊，請參閱[取得 Microsoft Azure 免費試用](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **安全殼層 (SSH) 用戶端**：SSH 用戶端可用來從遠端連線至 HDInsight 叢集，並直接在叢集上執行命令。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。
* **SSH 金鑰 (選擇性)**：您可以使用密碼或公開金鑰，保護用來連線至叢集的 SSH 帳戶。 使用密碼比較簡單，因為您不需要建立公開/私密金鑰組即可開始使用。 不過，使用金鑰更加安全。

  > [!NOTE]
  > 本文中的步驟假設您使用密碼。


## <a name="automate-cluster-creation"></a>自動建立叢集

您可以使用 Azure Resource Manager 範本、SDK 以及 PowerShell，自動建立 R Server for HDInsight 執行個體。

* 若要使用 Azure Resource Manager 範本來建立 R 伺服器執行個體，請參閱[部署 R 伺服器 HDInsight 叢集](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/)。
* 若要使用 .NET SDK 建立 R 伺服器執行個體，請參閱[在 HDInsight 中使用 .NET SDK 建立以 Linux 為基礎的叢集](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)。
* 若要使用 PowerShell 部署 R 伺服器，請參閱[使用 Azure PowerShell 在 HDInsight 中建立以 Linux 為基礎的叢集](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)。


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>使用 Azure 入口網站建立叢集

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 依序選取 [新增] > [情報 + 分析] > [HDInsight]。

    ![建立新叢集的影像](./media/r-server-get-started/newcluster.png)

3. 在 [快速建立] 體驗中，於 [叢集名稱] 方塊中輸入叢集的名稱。 如果您有多個 Azure 訂用帳戶，請使用 [訂用帳戶] 方塊選取要使用的訂用帳戶。

    ![叢集名稱和訂用帳戶選取項目](./media/r-server-get-started/clustername.png)

4. 選取 [叢集類型] 以開啟 [叢集組態] 窗格。 在 [叢集組態] 窗格中，選取下列選項︰

    * **叢集類型**：選取 [R 伺服器]。
    * **版本**︰選取要安裝在叢集上的 R 伺服器版本。 目前可用的版本是 **R 伺服器 9.1 (HDI 3.6)**。 R 伺服器可用版本的版本資訊可在 [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91) 上取得。
    * **R 伺服器的 R Studio Community 版本**︰邊緣節點上依預設會安裝這個以瀏覽器為基礎的 IDE。 如果您不想加以安裝，請清除此核取方塊。 如果您選擇加以安裝，則在叢集建立後，您將可在叢集的入口網站應用程式窗格中找到用來存取 RStudio 伺服器登入的 URL。
    * 將其他選項保留為預設值，然後使用 [選取] 按鈕儲存叢集類型。

        ![[叢集類型] 窗格的螢幕擷取畫面](./media/r-server-get-started/clustertypeconfig.png)

5. 在 [基本] 窗格的 [叢集登入使用者名稱] 和 [叢集登入密碼] 方塊中，分別輸入叢集的使用者名稱和密碼。

6. 在 [安全殼層 (SSH) 使用者名稱] 方塊中，指定 SSH 使用者名稱。 透過 SSH 用戶端從遠端連線至叢集時，會使用 SSH。 您可以在此方塊中指定 SSH 使用者，也可以在叢集建立之後指定 (於叢集的 [設定] 索引標籤中)。
   
   > [!NOTE] 
   > R 伺服器的設定預期會有 “remoteuser” 的 SSH 使用者名稱。 如果您使用不同的使用者名稱，就必須在叢集建立之後執行額外步驟。

7. 除非您偏好使用公開金鑰，否則請將 [使用與叢集登入相同的密碼] 核取方塊保持勾選狀態，以使用 [密碼] 作為驗證類型。 您需要公開/私密金鑰組，以透過遠端用戶端 (例如 Visual Studio R 工具、RStudio 或其他桌面 IDE) 存取叢集上的 R 伺服器。 如果您安裝 RStudio Server Community 版本，則必須選擇 SSH 密碼。     

   若要建立並使用公開/私密金鑰組，請清除 [使用與叢集登入相同的密碼]。 然後選取 [公開金鑰]，並依照下列說明繼續操作。 這些指示會假設您已安裝 Cygwin 和 ssh-keygen 或具同等效力的命令。

   a. 從膝上型電腦上的命令提示字元產生公開/私密金鑰組︰

        ssh-keygen -t rsa -b 2048

   b. 依照提示來為金鑰檔命名，然後輸入複雜密碼來提高安全性。 您的畫面應該看起來如下圖所示：

      ![Windows 中的 SSH 命令列](./media/r-server-get-started/sshcmdline.png)

      此命令會以 <private-key-filename>.pub 名稱建立私密金鑰檔案和公開金鑰檔案。 在此範例中，這兩個檔案分別是 furiosa 和 furiosa.pub：

      ![dir 命令的範例結果](./media/r-server-get-started/dir.png)

   c. 在指派 HDI 叢集認證時指定公開金鑰檔案 (&#42;.pub)。 請確認您的資源群組和區域，然後選取 [下一步]。

      ![認證窗格](./media/r-server-get-started/publickeyfile.png)  

   d. 在膝上型電腦上變更私密金鑰檔案的權限：

        chmod 600 <private-key-filename>

   e. 使用私密金鑰檔案搭配 SSH 進行遠端登入：

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      或者，在用戶端上將私密金鑰檔案納入為 R 伺服器的 Hadoop Spark 計算內容定義的一部分。 如需詳細資訊，請參閱[建立 Spark 的計算內容](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark)。

8. 快速建立會讓您切換至 [儲存體] 窗格。 在此處，您可以選取叢集所使用之 HDFS 檔案系統的主要位置所要使用的儲存體帳戶設定。 選取新的或現有的 Azure 儲存體帳戶，或選取現有的 Azure Data Lake Store 帳戶。

    - 如果您選取 Azure 儲存體帳戶，您可以藉由選取 [選取儲存體帳戶] 和相關帳戶，來選擇現有的帳戶。 使用 [選取儲存體帳戶] 區段中的 [新建] 連結，可建立新的帳戶。

      > [!NOTE]
      > 如果您選取 [新增]，則必須輸入新儲存體帳戶的名稱。 如果可接受該名稱，就會出現綠色核取記號。

      [預設容器] 的預設值為叢集的名稱。 請不要更動此預設值。

      如果您選取新的儲存體帳戶，請使用提示中的 [位置] 選取其區域。  

         ![資料來源窗格](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > 選取預設資料來源位置的同時，也會設定 HDInsight 叢集位置。 叢集和預設資料來源必須位於相同區域中。

    - 如果您要使用現有的 Data Lake Store 帳戶，請選取所要使用的帳戶。 然後，將叢集 *ADD* 身分識別新增至叢集，以允許它對存放區的存取。 如需此程序的詳細資訊，請參閱[使用 Azure 入口網站建立具有 Data Lake Store 的 HDInsight 叢集](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)。

    使用 [選取]  按鈕以儲存資料來源設定。


9. 接著會顯示 [摘要] 窗格，以供您驗證所有的設定。 您可以在這裡變更叢集大小，以修改叢集中的伺服器數目。 您也可以指定任何您想要執行的指令碼動作。 除非您確定需要更大的叢集，否則請保留背景工作節點數目的預設值 **4**。 此窗格也會顯示該叢集的預估成本。

    ![叢集摘要](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > 如有需要，您可以在稍後透過入口網站來重新調整叢集的大小 ([叢集] > [設定] > [調整叢集])，以增加或減少背景工作節點的數目。 此大小調整有助於讓未使用的叢集閒置下來，或增加容量以滿足大型工作的需求。
   >
   >

   在調整叢集、資料節點和邊緣節點的大小時，所應考量的因素包括︰  

   * 資料很大時，Spark 上分散式 R 伺服器分析的效能與背景工作節點數目成正比。  

   * R 伺服器分析的效能與分析的資料大小呈線性關係。 例如︰  

     * 對於小型到中型的資料，在邊緣節點上的本機計算內容中分析資料時，會獲得最佳效能。 如需關於本機和 Spark 計算內容在哪些情況下運作效能最佳的詳細資訊，請參閱[適用於 HDInsight 上 R 伺服器的計算內容選項](r-server-compute-contexts.md)。<br>
     * 如果您登入邊緣節點，並執行 R 指令碼，則會在邊緣節點上「本機」執行 ScaleR rx- 以外的所有函式。 因此，邊緣節點的記憶體和核心數目應據以調整大小。 如果您在 HDI 上將 R Server 當做膝上型電腦的遠端計算內容，也適用相同原則。

   使用 [選取]  按鈕以儲存節點定價設定。

   ![節點定價層的窗格](./media/r-server-get-started/pricingtier.png)

   還有 [下載範本和參數] 的連結。 選取此連結，會顯示可用來以選取的設定自動建立叢集的指令碼。 在您的叢集建立之後，您也可透過叢集的 Azure 入口網站項目取得這些指令碼。

   > [!NOTE]
   > 建立叢集需要一些時間，通常約 20 分鐘左右。 若要查看建立進度，請使用 [開始面板] 上的圖格，或頁面左側的 [通知] 項目。
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>連線到 RStudio 伺服器

如果您已選擇要在安裝中包含 RStudio Server Community 版本，您可以透過兩種方法存取 RStudio 登入：

- 移至下列 URL (其中，CLUSTERNAME 是您所建立之叢集的名稱)：

    https://*CLUSTERNAME*.azurehdinsight.net/rstudio/

- 在 Azure 入口網站中開啟叢集項目，選取 [R 伺服器儀表板] 快速連結，然後選取 [R Studio 儀表板]︰

  ![存取 RStudio 儀表板](./media/r-server-get-started/rstudiodashboard1.png)

  ![存取 RStudio 儀表板](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> 不論使用哪一種方法，第一次登入時都必須驗證兩次。 第一次驗證時，請提供叢集管理員的使用者識別碼和密碼。 第二次提示時，則提供 SSH 使用者識別碼和密碼。 之後再登入時，只需要提供 SSH 使用者識別碼和密碼。

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>連線到 R Server 邊緣節點

使用 SSH 透過此命令連線至 HDInsight 叢集的 R 伺服器邊緣節點：

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> 您可以在 Azure 入口網站中找到 `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` 位址。 選取您的叢集，然後選取 [所有設定] > [應用程式] > [RServer]。 這會顯示邊緣節點的 SSH 端點資訊。
>
> ![邊緣節點之 SSH 端點的影像](./media/r-server-get-started/sshendpoint.png)
>
>

如果您使用密碼保護您 SSH 使用者帳戶的安全，系統會提示您輸入密碼。 如果您使用公開金鑰，您可能必須使用 `-i` 參數來指定對應的私密金鑰。 例如︰

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

連線之後，您將看見類似以下的提示：

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>啟用多個並行使用者

藉由為 RStudio 社群版本執行所在的邊緣節點新增更多使用者，即可啟用多個並行使用者。

當您建立 HDInsight 叢集時，您必須提供兩個使用者 (HTTP 使用者和 SSH 使用者)。

![輸入叢集使用者和 SSH 使用者的認證](./media/r-server-get-started/concurrent-users-1.png)

- **叢集登入使用者名稱**：透過 HDInsight 閘道 (用來保護您所建立的 HDInsight 叢集) 進行驗證的 HTTP 使用者。 此 HTTP 使用者可用來存取 Ambari UI、YARN UI，以及其他 UI 元件。
- **安全殼層 (SSH) 使用者名稱**：透過安全殼層存取叢集的 SSH 使用者。 此使用者是在 Linux 系統中適用於所有前端節點、背景工作節點和邊緣節點的使用者。 因此您可以使用 SSH 來存取遠端叢集中的任何節點。

用於 HDInsight 叢集上 Microsoft R Server 中的 RStudio Server Community 版本，只接受 Linux 使用者名稱和密碼作為登入機制。 但不支援傳遞權杖。 因此如果您已建立新叢集，而且想要使用 RStudio 來存取它，您需要登入兩次。

1. 透過 HDInsight 閘道使用 HTTP 使用者認證登入： 

    ![輸入 HTTP 使用者認證](./media/r-server-get-started/concurrent-users-2a.png)

2. 使用 SSH 使用者認證登入 RStudio：
  
    ![輸入 SSH 使用者認證](./media/r-server-get-started/concurrent-users-2b.png)

目前，在佈建 HDInsight 叢集時，您只能建立一個 SSH 使用者帳戶。 因此若要讓多個使用者存取 HDInsight 叢集上的 Microsoft R Server，您必須在 Linux 系統中建立其他使用者。

由於 RStudio Server Community 版本執行於叢集的邊緣節點上，因此有以下三個步驟：

1. 使用已建立的 SSH 使用者登入邊緣節點。
2. 在邊緣節點中新增更多 Linux 使用者。
3. 使用 RStudio Community 版本與已建立的使用者。

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>使用所建立的 SSH 使用者來登入邊緣節點

下載任何 SSH 工具 (例如 PuTTY)，並使用現有的 SSH 使用者進行登入。 然後遵循[使用 SSH 連線到 HDInsight (Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md) 中提供的指示來存取邊緣節點。 HDInsight 叢集上 R 伺服器的邊緣節點位址是：**clustername-ed-ssh.azurehdinsight.net**


### <a name="add-more-linux-users-to-the-edge-node"></a>在邊緣節點中新增更多 Linux 使用者

若要將使用者新增至邊緣節點，請執行下列命令︰

    sudo useradd yournewusername -m
    sudo passwd yourusername

您應該會看見下列傳回的項目： 

![sudo 命令的輸出](./media/r-server-get-started/concurrent-users-3.png)

當系統提示您輸入目前的 Kerberos 密碼時，請選取 Enter 鍵加以忽略即可。 `useradd` 命令中的 `-m` 選項表示系統將為使用者建立主資料夾。 RStudio Community 版本需要此資料夾。


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>使用 RStudio Community 版本與已建立的使用者

使用已建立的使用者登入 RStudio：

![RStudio 登入頁面](./media/r-server-get-started/concurrent-users-4.png)

請注意，RStudio 會指出您將使用新的使用者 (例如，此範例中的 **sshuser6**) 登入叢集： 

![新使用者在 RStudio 命令列上的位置](./media/r-server-get-started/concurrent-users-5.png)

您也可以使用原始認證 (預設為 **sshuser**)，從其他瀏覽器視窗同時登入。

您可以使用 ScaleR 函式來提交作業。 以下是用來執行作業的命令範例：

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context
    mySparkCluster <- RxSpark()

    # Set the compute context
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary
    summary(modelSpark)


請注意，提交的作業位於 YARN UI 中的不同使用者名稱下：

![YARN UI 中的使用者清單](./media/r-server-get-started/concurrent-users-6.png)

另請注意，新增的使用者在 Linux 系統中不具備根權限。 但他們具備在遠端 HDFS 檔案系統和 Blob 儲存體中存取所有檔案的相同權限。


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>使用 R 主控台

1. 在 SSH 工作階段中，使用以下命令啟動 R 主控台：  

        R

2. 您應該會看到如下所示的輸出：
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.
        >

3. 您可以透過 `>` 提示，輸入 R 程式碼。 R 伺服器包含可讓您輕鬆與 Hadoop 互動並執行分散式計算的套件。 例如，若要檢視 HDInsight 叢集的預設檔案系統根目錄，可使用下列命令：

        rxHadoopListFiles("/")

4. 您也可以使用 Blob 儲存體樣式的定址：

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>從 Microsoft R Server 或 Microsoft R Client 的遠端執行個體使用 HDI 上的 R 伺服器

您可以設定從桌上型電腦或膝上型電腦上執行的 Microsoft R Server 或 Microsoft R Client 遠端執行個體，來存取 HDI Hadoop Spark 計算內容。 如需詳細資訊，請參閱[建立 Spark 的計算內容](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)中的＜使用 Microsoft R Server 作為 Hadoop 用戶端＞一節。 若要這樣做，請在膝上型電腦上定義 RxSpark 計算內容時指定下列選項︰hdfsShareDir、shareDir、sshUsername、sshHostname、sshSwitches 和 sshProfileScript。 以下是範例：


    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )


## <a name="use-a-compute-context"></a>使用計算內容

您可以使用計算內容來控制是要在邊緣節點上本機執行計算，還是將計算散發到 HDInsight 叢集的各個節點。

1. 在 RStudio Server 或 R 主控台 (在 SSH 工作階段中) 中，使用下列程式碼將範例資料載入 HDInsight 的預設儲存體中：

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. 建立一些資料資訊，並定義兩個資料來源，以便您使用資料：

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. 使用本機計算內容執行資料的羅吉斯迴歸：

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    您應該會看到結尾類似以下幾行的輸出：

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. 使用 Spark 內容來執行相同的羅吉斯迴歸。 Spark 內容會將處理作業散發到 HDInsight 叢集的所有背景工作節點中。

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > 您也可以使用 MapReduce，將計算分散到叢集節點。 如需計算內容的詳細資訊，請參閱[適用於 HDInsight 中 R 伺服器的計算內容選項](r-server-compute-contexts.md)。


## <a name="distribute-r-code-to-multiple-nodes"></a>將 R 程式碼分散到多個節點

使用 R 伺服器時，您可以輕鬆採用現有的 R 程式碼並利用 `rxExec` 跨多個叢集節點執行。 在執行參數掃掠或模擬時，此函式有其效用。 以下是使用 `rxExec` 的範例程式碼：

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

如果您仍使用 Spark 或 MapReduce 內容，此命令會針對執行程式碼 `(Sys.info()["nodename"])` 的背景工作節點傳回 `nodename` 值。 例如，在四節點叢集上時，您應會取得如下的輸出：

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="access-data-in-hive-and-parquet"></a>存取 Hive 和 Parquet 中的資料

R 伺服器 9.1 版所提供的功能，可讓您直接存取 Hive 和 Parquet 中的資料，以供 ScaleR 函式用於 Spark 計算內容中。 這些功能可透過新的 ScaleR 資料來源函式 (稱為 RxHiveData 和 RxParquetData) 來使用。 藉由使用 Spark SQL 將資料直接載入到 Spark 資料框架供 ScaleR 進行分析，即可讓這些函式運作。  

以下程式碼提供關於新函式使用方式的一些範例︰

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


如需關於這些新函式的詳細資訊，請使用 `?RxHivedata` 和 `?RxParquetData` 命令參閱 R 伺服器中的線上說明。  


## <a name="install-additional-r-packages-on-the-edge-node"></a>在邊緣節點上安裝其他 R 套件

如果您想要在邊緣節點上安裝其他 R 套件，您可以在透過 SSH 連線至邊緣節點時，直接從 R 主控台內使用 `install.packages()`。 不過，如果您需要在叢集的背景工作節點上安裝 R 套件，就必須使用指令碼動作。

指令碼動作是一種 Bash 指令碼，可用來變更 HDInsight 叢集的設定或安裝其他軟體，例如其他 R 套件。 若要使用指令碼動作安裝其他套件，請執行下列步驟。

> [!IMPORTANT]
> 只有在叢集建立後，才可以使用指令碼動作安裝其他 R 套件。 在叢集建立期間請勿使用此程序，因為指令碼相依於已完整安裝並設定的 R 伺服器。
>
>

1. 從 [Azure 入口網站](https://portal.azure.com)選取您 HDInsight 叢集上的 R Server。

2. 在 [設定] 窗格中，選取 [指令碼動作] > [提交新的]。

   ![指令碼動作窗格的影像](./media/r-server-get-started/scriptaction.png)

3. 在 [提交指令碼動作] 窗格中，提供下列資訊：

   * **名稱**︰用來識別此指令碼的易記名稱。

   * **Bash 指令碼 URI**：`http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **前端**︰此項目應清除。

   * **背景工作**︰此項目應清除。

   * **Zookeeper**︰此項目應清除。

   * **邊緣節點**︰此項目應選取。

   * **參數**︰要安裝的 R 套件，例如 `bitops stringr arules`。

   * **保存此指令碼**︰此項目應選取。  

   > [!NOTE]
   > 根據預設，會使用與已安裝的 R 伺服器同版本的 Microsoft R Application Network 存放庫的快照安裝所有 R 套件。 如果您想要安裝較新版的套件，則會有不相容的風險。 不過，將 `useCRAN` 指定為套件清單的第一個元素 (例如 `useCRAN bitops, stringr, arules`)，這種安裝就可行。  
   > 
   > 有些 R 套件需要額外的 Linux 系統程式庫。 為了方便起見，我們已預先安裝前 100 個常用 R 套件所需的相依性。 如果您安裝的 R 套件所需的程式庫不在其中，則必須下載此處所使用的基底指令碼，並加入安裝系統程式庫的步驟。 接下來，您必須將修改過的指令碼上傳至 Azure 儲存體中的公用 Blob 容器，並使用修改過的指令碼來安裝套件。
   >
   > 如需開發指令碼動作的詳細資訊，請參閱[指令碼動作開發](../hdinsight-hadoop-script-actions-linux.md)。  
   >
   >

   ![新增指令碼動作](./media/r-server-get-started/submitscriptaction.png)

4. 按一下 [建立] 執行指令碼。 指令碼完成後，即可在所有背景工作節點上使用 R 套件。


## <a name="configure-microsoft-r-server-operationalization"></a>設定 Microsoft R Server 運算化

完成資料模型化時，可以運作模型以進行預測。 若要設定 Microsoft R Server 運算化，請執行下列步驟：

1. 對邊緣節點使用 `ssh` 命令，例如： 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. 變更相關版本的目錄，並對 .dll 檔案使用 `sudo dotnet` 命令。 

   對於 Microsoft R Server 9.1：

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   對於 Microsoft R Server 9.0：

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. 若要使用一整體設定來設定 Microsoft R Server 運算化，請執行下列作業：

   a. 選取 `Configure R Server for Operationalization`。

   b. 選取 `A. One-box (web + compute nodes)`。

   c. 輸入 `admin` 使用者的密碼。

   ![一整體運算化](./media/r-server-get-started/admin-util-one-box-.png)

4. 您也可以選擇性地執行診斷測試，如下所示：

   a. 選取 `6. Run diagnostic tests`。

   b. 選取 `A. Test configuration`。

   c. 輸入使用者名稱 `admin`，並輸入上一個設定步驟中的密碼。

   d. 確認 `Overall Health = pass`。

   e. 結束系統管理公用程式。

   f. 結束 SSH。

   ![運算化的診斷](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>如果您在 Spark 計算內容中嘗試取用使用 mrsdeploy 函式建立的 Web 服務時遇到長時間延遲的狀況，您可能需要新增一些遺漏的資料夾。 每當使用 mrsdeploy 函式從 Web 服務叫用 Spark 應用程式時，該應用程式都會屬於名為 rserve2 的使用者。 若要解決此問題：

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


在此階段中，運算化的設定已完成。 現在，您可以使用 R 用戶端上的 mrsdeploy 套件連線至邊緣節點上的運算化。 接著您即可開始使用其功能，例如[遠端執行](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely)和 [Web 服務](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)。 您可能必須設定透過 SSH 登入的連接埠轉送通道，端視您的叢集是否設定在虛擬網路上而定。

### <a name="r-server-cluster-on-a-virtual-network"></a>虛擬網路上的 R 伺服器叢集

確定您允許流量通過連接埠 12800 到達邊緣節點。 這樣一來，您就可以使用邊緣節點連線至運算化功能。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


如果 `remoteLogin()` 無法連線至邊緣節點，但您可以使用 SSH 連線至邊緣節點，則必須確認是否已正確設定在連接埠 12800 上允許流量的規則。 如果您持續遇到此問題，您可以藉由設定透過 SSH 的連接埠轉送通道來處理此問題。 如需相關指示，請參閱下一節。

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>R 伺服器叢集未設定於虛擬網路上

如果您的叢集未設定於虛擬網路上，或如果您在透過虛擬網路進行連線時遇到問題，您可以使用 SSH 連接埠轉送通道︰

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

您也可以在 PuTTY 上加以設定：

![PuTTY SSH 連線](./media/r-server-get-started/putty.png)

SSH 工作階段變為作用中後，來自電腦連接埠 12800 的流量就會透過 SSH 工作階段轉送到邊緣節點的連接埠 12800。 請務必在 `remoteLogin()` 方法中使用 `127.0.0.1:12800`。 此方法會透過連接埠轉送登入邊緣節點的運算化。


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>在 HDInsight 背景工作節點上調整 Microsoft R 伺服器運算化的計算節點

### <a name="decommission-the-worker-nodes"></a>將背景工作節點解除委任

Microsoft R 伺服器目前無法透過 Yarn 管理。 如果未將背景工作節點解除委任，Yarn ResourceManager 將無法如預期般運作，因為它會無法辨識伺服器目前所使用的資源。 為避免發生此狀況，建議您在相應放大計算節點之前，將背景工作節點解除委任。

若要將背景工作節點解除委任：

1. 登入 HDI 叢集的 Ambari 主控台，然後選取 [主機] 索引標籤。
2. 選取要解除委任的背景工作節點，然後選取 [動作] > [選取的主機] > [主機] > [開啟維護模式]。 例如，在下列影像中，我們選取了要解除委任 wn3 和 wn4。  

   ![開啟維護模式之命令的螢幕擷取畫面](./media/r-server-get-started/get-started-operationalization.png)  

3. 選取 [動作] > [選取的主機] > [DataNode] > [解除委任]。
4. 選取 [動作] > [選取的主機] > [NodeManagers] > [解除委任]。
5. 選取 [動作] > [選取的主機] > [DataNode] > [停止]。
6. 選取 [動作] > [選取的主機] > [NodeManagers] > [停止]。
7. 選取 [動作] > [選取的主機] > [主機] > [停止所有元件]。
8. 將背景工作節點取消選取，並選取前端節點。
9. 選取 [動作] > [選取的主機] > [主機] > [重新啟動所有元件]。

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>在每個已解除委任的背景工作節點上設定計算節點

1. 使用 SSH 以連線至每個已解除委任的背景工作節點。
2. 使用 `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll` 執行系統管理公用程式。
3. 輸入 `1` 以選取選項 `Configure R Server for Operationalization`。
4. 輸入 `c` 以選取選項 `C. Compute node`。 此步驟會設定背景工作節點上的計算節點。
5. 結束系統管理公用程式。

### <a name="add-compute-nodes-details-on-the-web-node"></a>在 Web 節點上新增計算節點的詳細資料

在所有已解除委任的背景工作節點皆設定為在計算節點上執行後，請回到邊緣節點，然後在 Microsoft R Server Web 節點的設定中新增已解除委任之背景工作節點的 IP 位址︰

1. 使用 SSH 連線到邊緣節點。
2. 執行 `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`。
3. 尋找 `URIs` 區段，並新增背景工作節點的 IP 和連接埠詳細資料。

    ![邊緣節點的命令列](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>疑難排解

如果您在建立 HDInsight 叢集時遇到問題，請參閱[存取控制需求](../hdinsight-administer-use-portal-linux.md#create-clusters)。


## <a name="next-steps"></a>後續步驟

現在，您已了解如何建立包含 R 伺服器的 HDInsight 叢集。 您也已了解從 SSH 工作階段使用 R 主控台的基本概念。 下列主題說明的其他方式，可用來管理和使用 HDInsight 上的 R 伺服器：

* [適用於 HDInsight 上 R 伺服器的計算內容選項](r-server-compute-contexts.md)
* [適用於 HDInsight R 伺服器的 Azure 儲存體選項](r-server-storage.md)
