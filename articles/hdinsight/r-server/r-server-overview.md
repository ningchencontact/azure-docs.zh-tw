---
title: Azure HDInsight 上的 R 伺服器簡介 | Microsoft Docs
description: 了解如何使用 HDInsight 上的 R 伺服器來建立適用於巨量資料分析的應用程式。
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 19c286db9a8a2aa537badc83d98a1b74b73e9873
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>HDInsight 上的 R 伺服器與開放原始碼 R 功能簡介

在 Azure 中建立 HDInsight 叢集時，可選擇 Microsoft R 伺服器 (也稱為 Microsoft Machine Learning Server) 作為部署選項。 提供此選項的叢集類型稱為 **R 伺服器**。 這項功能可讓資料科學家、統計學家以及 R 程式設計人員隨其所需存取 HDInsight 上可調整大小的分散式分析方法。

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

HDInsight 上的 R 伺服器所提供的最新功能，適用於幾乎任何大小的資料集上進行之以 R 為基礎的分析，且不論資料集是載入到 Azure Blob 或 Data Lake 儲存體。 R 伺服器叢集是根據開放原始碼 R 所建置，因此您建置的 R 型應用程式可以利用 8000 多個開放原始碼 R 套件中的任何一個。 ScaleR 中的常式與 R 伺服器中包含的 Microsoft 巨量資料分析套件亦可用。

叢集的邊緣節點提供便利的地方，以便連線到叢集以及執行 R 指令碼。 有了邊緣節點之後，即可選擇跨邊緣節點伺服器的核心，執行 ScaleR 的平行分散式函數。 您也可以使用 ScaleR 的 Hadoop Map Reduce 或 Spark 計算內容，跨越叢集的節點來執行這些函數。

可以下載分析所產生的模型或預測，以便在內部部署使用。 它們也可以在 Azure 中的其他地方實際運作，特別是透過 [Azure Machine Learning Studio](http://studio.azureml.net) [Web 服務](../../machine-learning/studio/publish-a-machine-learning-web-service.md)。

## <a name="get-started-with-r-server-on-hdinsight"></a>開始在 HDInsight 中使用 R 伺服器

若要在 Azure HDInsight 叢集中包含 R 伺服器叢集，請在使用 Azure 入口網站建立 HDInsight 叢集時選取 **R 伺服器**叢集類型。 R 伺服器叢集類型包括在叢集資料節點上的 R 伺服器，以及在邊緣節點上的 R 伺服器，可當作以 R 伺服器為基礎之分析的登陸區域。 請參閱[開始使用 HDInsight 上的 R 伺服器](r-server-get-started.md)，以了解有關如何建立叢集的逐步解說。

## <a name="why-choose-r-server-in-hdinsight"></a>為何選擇 HDInsight 中的 R 伺服器？

HDInsight 中的 R 伺服器提供在 Azure 上使用 R 伺服器的選項。 R 伺服器包括：

+ **根據 Microsoft 和開放原始碼的最佳 AI 創新**

  Microsoft 致力於讓每個人和每個組織都可以存取 AI。 R 伺服器包括一組豐富的高擴充性、分散式演算法，例如 [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)、[revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) 及 [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package)，可用於大小比實體記憶體還要大的資料，並且可以分散式方式在各種平台上執行。 深入了解產品隨附之 Microsoft 自訂 [R 套件](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)和 [Python 套件](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)的集合。
  
  R 伺服器會在單一企業等級平台上，將這些 Microsoft 創新和來自開放原始碼社群 (R、Python 及 AI 工具組) 都橋接在一起。 任何 R 或 Python 開放原始碼機器學習套件都可以與任何來自 Microsoft 的專屬創新搭配運作。 

+ **簡單、安全且高擴充性的運作和管理**

  依賴傳統運作範例和環境的企業最終還是會朝這個領域投入許多時間和精力。 痛點通常會導致成本和延遲擴大，包括：模型的轉譯時間、讓它們保持有效及最新狀態的反覆項目、法規核准、管理整個運作的權限。

  R 伺服器提供最高等級的[運作](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) - 從機器學習模型完成的時間開始，只要點擊幾下就可以產生 Web 服務 API。 這些 [Web 服務](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)裝載於內部部署伺服器方格或雲端中，而且可以與企業營運應用程式整合。 部署到彈性方格的能力可讓您根據您的商務需求 (針對批次和即時評分) 順暢地進行調整。 如需指示，請參閱[讓 HDInsight 上的 R Server 叢集能運作](r-server-operationalize.md)。

+ **深度生態系統參與來傳遞具有最佳整體擁有成本的客戶成功**

  著手讓應用程式有智慧或者只是想要學習 AI 和機器學習新知的個人，需要正確的資源來協助他們開始。 除了本文件之外，Microsoft 還提供數個學習資源並且配合數個訓練合作夥伴，協助您快速提升技能及提高生產力。


## <a name="key-features-of-r-server-on-hdinsight"></a>HDInsight 上的 R 伺服器之主要功能

HDInsight 上的 R 伺服器包含下列功能。

| 功能分類 | 說明 |
|------------------|-------------|
| 已啟用 R | [R 套件](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)適用於以 R 撰寫的解決方案，具有 R 的開放原始碼散佈和用於執行指令碼的執行階段基礎結構。 |
| 已啟用 Python | [Python 套件](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference)適用於以 Python 撰寫的解決方案，具有 Python 的開放原始碼散佈和用於執行指令碼的執行階段基礎結構。  
| [預先定型的模型](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | 針對視覺化分析和文字情感分析，準備好要對您提供的資料進行評分。 |
| [部署和取用](r-server-operationalize.md) | 讓您的伺服器能運作，並將解決方案部署為 Web 服務。 |
| [遠端執行](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-server-or-client) | 從用戶端工作站上，在您網路上的 R 伺服器啟動遠端工作階段。 |


## <a name="data-storage-options-for-r-server-on-hdinsight"></a>適用於 HDInsight 上的 R 伺服器之資料儲存體選項

HDInsight 叢集 HDFS 檔案系統的預設儲存體可以與 Azure 儲存體帳戶或 Azure Data Lake Store 關聯。 此關聯可確保在分析期間，上傳至叢集儲存體的任何資料皆保有永續性，而且即使刪除叢集之後還是可以取用資料。 有各種工具可用於處理將資料傳輸到您所選儲存體選項的作業，包括儲存體帳戶的入口網站型上傳工具，以及 [AzCopy](../../storage/common/storage-use-azcopy.md) 公用程式。

不論使用中的儲存體選項為何，您都可以選擇在叢集佈建程序期間，新增其他 Blob 和 Data Lake 儲存體的存取權。 如需有關新增其他帳戶存取權的詳細資訊，請參閱[開始使用 HDInsight 上的 R 伺服器](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started)。 若要深入了解如何使用多個儲存體帳戶，請參閱補充性[適用於 HDInsight 上 R 伺服器的 Azure 儲存體選項](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage)文章。

您也可以將 [Azure 檔案](../../storage/files/storage-how-to-use-files-linux.md)作為在邊緣節點上使用的儲存體選項。 Azure 檔案可讓您將建立於 Azure 儲存體的檔案共用掛接至 Linux 檔案系統。 如需適用於 HDInsight 叢集上 R 伺服器的這些資料儲存體選項詳細資訊，請參閱[適用於 HDInsight 上的 R 伺服器的 Azure 儲存體選項](r-server-storage.md)。

## <a name="access-machine-learning-server-on-the-cluster"></a>在叢集上存取 Machine Learning Server

您可以使用瀏覽器連線到邊緣節點上的 Microsoft Machine Learning Server。 它預設會在叢集建立期間安裝。 如需詳細資訊，請參閱[開始使用 HDInsight 上的 R 伺服器](r-server-get-started.md)。 您也可以使用 SSH/PuTTY 來存取 R 主控台，從命令列連線到 ML Server。 

## <a name="develop-and-run-r-scripts"></a>開發和執行 R 指令碼

您所建立與執行的 R 指令碼，可以任意運用 8000 多種開放原始碼 R 套件，以及 ScaleR 程式庫中的平行與分散式常式。 一般而言，以 R 伺服器在邊緣節點上執行的指令碼會在該節點上的 R 解譯器內執行。 但是必須以設定為 Hadoop Map Reduce (RxHadoopMR) 或 Spark (RxSpark) 之計算內容呼叫 ScaleR 函數的那些步驟除外。 在此情況中，函數會以分散方式，跨越與參考資料相關聯之叢集的那些資料 (工作) 節點執行。 如需不同計算內容選項的詳細資訊，請參閱[適用於 HDInsight 上 R 伺服器的計算內容選項](r-server-compute-contexts.md)。

## <a name="operationalize-a-model"></a>模型運作

完成資料模型化時，可以在 Azure 中或內部部署中運作模型，以為新的資料進行預測。 這個程序稱為評分。 評分可在 HDInsight、Azure Machine Learning 或內部部署中完成。

### <a name="score-in-hdinsight"></a>在 HDInsight 中評分
若要在 HDInsight 中評分，可以針對已載入至儲存體帳戶的新資料檔案，撰寫可呼叫模型的 R 函數以進行預測。 然後將預測存回儲存體帳戶。 您可以視需要在叢集的邊緣節點上執行這個常式，或使用排程作業來進行。

### <a name="score-in-azure-machine-learning-aml"></a>Azure Machine Learning 中的評分 (AML)
若要使用 Azure Machine Learning 來評分，請使用 Azure Machine Learning R 套件開放原始碼 (稱為 [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)) 來將您的模型發佈為 Azure Web 服務。 為了方便起見，此套件已預先安裝在邊緣節點。 接下來，使用 Azure Machine Learning 中的功能建立 Web 服務的使用者介面，然後呼叫評分所需的 Web 服務。

如果您選擇這個選項，就必須將所有 ScaleR 模型物件轉換成對等的開放原始碼模型物件，才可搭配 Web 服務使用。 針對此轉換，您可以使用 ScaleR 強制型轉函數 (例如，適用於集成模型的 `as.randomForest()`) 來完成。

### <a name="score-on-premises"></a>內部部署評分
若要在建立模型之後進行內部部署評分，可以在 R 中將模型序列化，下載它、將它還原序列化，然後使用它進行新資料的評分。 您可以使用上述[在 HDInsight 中評分](#scoring-in-hdinsight)所描述的方法，或使用 [DeployR](https://deployr.revolutionanalytics.com/) 進行新資料的評分。

## <a name="maintain-the-cluster"></a>維護叢集

### <a name="install-and-maintain-r-packages"></a>安裝及維護 R 套件

由於 R 指令碼大多數的步驟會在邊緣節點上執行，因此在邊緣節點上需要有大多數您所使用的 R 套件。 若要在邊緣節點上安裝其他 R 套件，可以在 R 中使用一般 `install.packages()` 方法。

如果您只是在整個叢集中使用來自 ScaleR 程式庫的常式，則通常不需要在資料節點上安裝其他 R 套件。 但是，您可能需要其他套件，以支援在資料節點上使用 **rxExec** 或 **RxDataStep** 執行。

在這種情況下，您可以在建立叢集之後，使用指令碼動作來安裝其他套件。 如需詳細資訊，請參閱[管理 HDInsight 叢集中的 R 伺服器](r-server-hdinsight-manage.md)。

### <a name="change-hadoop-mapreduce-memory-settings"></a>變更 Hadoop MapReduce 記憶體設定

您可以在執行 MapReduce 作業時，修改叢集以變更 R 伺服器的可用記憶體數目。 若要修改叢集，請使用可透過叢集的 Azure 入口網站刀鋒視窗存取的 Apache Ambari UI。 如需如何存取叢集的 Ambari UI 的指示，請參閱[使用 Ambari Web UI 管理 HDInsight 叢集](../hdinsight-hadoop-manage-ambari.md)。

您也可以在 **RxHadoopMR** 的呼叫中使用 Hadoop 參數，變更 R 伺服器可用的記憶體數目，如下所示：

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>調整叢集的大小

您可以透過入口網站，相應增加或相應減少 HDInsight 上的現有 R 伺服器叢集。 藉由相應增加，您可以取得處理較大型工作所需的額外處理能力，或於叢集閒置時調回處理能力。 如需如何調整叢集規模的指示，請參閱[管理 HDInsight 叢集](../hdinsight-administer-use-portal-linux.md)。

### <a name="maintain-the-system"></a>維護系統

離峰期間，系統會在 HDInsight 叢集的基底 Linux VM 上執行維護，以套用 OS 修補程式和其他更新。 一般而言，維護作業會在每星期一和星期四上午 3:30 (以 VM 的本地時間為準) 進行。 執行更新時，系統會以一次僅影響四分之一以內叢集的方式進行。  

由於前端節點是備援節點，且並非所有資料節點都會受到影響，因此在這段期間執行的任何工作可能會變慢。 不過，應該都可執行完成。 除非發生需要重建叢集的嚴重失敗，否則您擁有的任何自訂軟體或本機資料，在這些維護事件中皆會保留。

## <a name="ide-options-for-r-server-on-an-hdinsight-cluster"></a>適用於 HDInsight 叢集上 R 伺服器的 IDE 選項

HDInsight 叢集的 Linux 邊緣節點，是 R 型分析的登陸區域。 HDInsight 最近的幾個版本，提供在邊緣節點上將 RStudio Server 安裝為瀏覽器型 IDE 的預設選項。 比起僅使用 R 主控台，使用 RStudio Server 當作開發及執行 R 指令碼的 IDE 可大幅提升生產力。

此外，您可以安裝電腦 IDE 並透過使用遠端 MapReduce 或 Spark 計算內容，使用它來存取叢集。 選項包括 Microsoft 的 [Visual Studio R 工具](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS)、RStudio 與 Walware 的 Eclipse 型 [StatET](http://www.walware.de/goto/statet)。

最後，透過 SSH 或 PuTY 連線，您就可以在 Linux 命令提示字元中輸入 **R** 來存取邊緣節點上的 R 主控台。 使用主控台介面時，在另一個視窗中針對 R 指令碼開發執行文字編輯器，並視需要剪下指令碼區段並貼上到 R 主控台中是非常方便的。

## <a name="pricing"></a>價格

與含 R 伺服器的 HDInsight 叢集相關聯的價格結構，類似於標準 HDInsight 叢集的價格。 以各種名稱、資料和邊緣節點的基礎 VM 大小為基準，再加上彈性工時加費。 如需關於 HDInsight 定價的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>後續步驟

若要深入了解如何使用 HDInsight 叢集上的 R 伺服器，請參閱下列主題：

* [開始使用 HDInsight 上的 R 伺服器叢集](r-server-get-started.md)
* [適用於 HDInsight 上的 R 伺服器叢集的計算內容選項](r-server-compute-contexts.md)
* [適用於 HDInsight 上 R Server 叢集的 Azure 儲存體選項](r-server-storage.md)
