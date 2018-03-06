---
title: "在 Azure Machine Learning 服務 (預覽) 中針對分類鳶尾花教學課程準備資料 | Microsoft Docs"
description: "這個完整的教學課程會示範如何使用 Azure Machine Learning 服務 (預覽) 端對端。 這是第一個部分，會討論資料準備工作。"
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 0bef557ee1394e3c786fd2c54e821b5dea28fabf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>教學課程：分類鳶尾花第 1 部分 - 準備資料

Azure Machine Learning 服務 (預覽) 是一套整合的端對端資料科學以及進階分析解決方案，可供專業資料科學家用來以雲端規模準備資料、開發測試以及部署模型。

本教學課程是三部分系列的第一部分。 在本教學課程，我們會逐步說明 Azure Machine Learning 服務 (預覽) 的基本概念。 您會了解如何：

> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中建立專案
> * 建立資料準備封裝
> * 產生 Python/PySpark 程式碼來叫用資料準備套件

本教學課程使用不受時間影響的[鳶尾花資料集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 螢幕擷取畫面是 Windows 專屬，但是 macOS 體驗幾乎完全相同。

## <a name="prerequisites"></a>先決條件

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

若要完成本教學課程，您必須具備：
- Azure Machine Learning 測試帳戶
- 已安裝 Azure Machine Learning Workbench

如果您還沒有上述項目，請遵循[快速入門：安裝和建立](quickstart-installation.md)一文中的指示來設定此帳戶，以及安裝 Azure Machine Learning Workbench 應用程式。 

## <a name="create-a-new-project-in-workbench"></a>在 Workbench 中建立新的專案

如果您遵循了[快速入門：安裝和啟動](quickstart-installation.md)一文中的步驟，您應該已經有這個專案，所以可以跳至下一節。

1. 開啟 Azure Machine Learning Workbench 應用程式，並登入 (如果需要)。 
   
   + 在 Windows 上，使用 **Machine Learning Workbench** 桌面捷徑來啟動它。 
   + 在 macOS 上，選取啟動列中的 **Azure ML Workbench**。

1. 在 [專案] 窗格中選取加號 (+)，然後選擇 [新增專案]。  

   ![新增工作區](media/tutorial-classifying-iris/new_ws.png)

1. 填妥表單欄位並選取 [建立] 按鈕，以在 Workbench 中建立新專案。

   欄位|教學課程的建議值|說明
   ---|---|---
   專案名稱 | myIris |輸入可識別您帳戶的唯一名稱。 您可以使用您自己的名稱，或最能識別測試的部門或專案名稱。 這個名稱長度應介於 2 到 32 個字元之間。 應該只包含英數字元及虛線 (-) 字元。 
   專案目錄 | c:\Temp\ | 指定要在其中建立專案的目錄。
   專案描述 | _保留空白_ | 適合用於描述專案的選擇性欄位。
   Visualstudio.com |_保留空白_ | 選擇性標題。 專案可在 Visual Studio Team Services 上選擇性地與 Git 存放庫相關聯，以便進行原始檔控制和共同作業。 [了解如何進行設定](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo)。 
   工作區 | IrisGarden (如果存在的話) | 選擇您在 Azure 入口網站中針對測試帳戶建立的工作區。 <br/>如果您已遵循快速入門，您應該有名稱為 IrisGarden 的工作區。 如果沒有，請選取您建立測試帳戶時所建立的工作區，或任何您想使用的其他工作區。
   專案範本 | 分類鳶尾花 | 範本包含您可用來瀏覽產品的指令碼和資料。 此範本包含您在本快速入門及此文件網站中的其他教學課程中需要使用的指令碼和資料。 

   ![新增專案](media/tutorial-classifying-iris/new_project.png)
 
 建立新的專案，專案儀表板隨即開啟並顯示該專案。 此時，您可以瀏覽專案首頁、資料來源、筆記本及原始程式檔。 

## <a name="create-a-data-preparation-package"></a>建立資料準備封裝

在本教學課程的這個部分中，您可以瀏覽資料並啟動資料準備程序。 當您在 Azure Machine Learning Workbench 中準備資料時，您在 Workbench 中執行之轉換的 JSON 表示法會儲存在本機資料準備套件 (*.dprep 檔案) 中。 此資料準備套件是您的資料準備工作在 Workbench 中的主要容器。

此資料準備套件可以遞交給執行階段執行，例如 local-C#/CoreCLR、Scala/Spark 或 Scala/HDI。 其中產生的程式碼可供適當的執行階段執行。 

1. 選取資料夾圖示，即可開啟檔案檢視，然後選取 **iris.csv** 來開啟該檔案。  

   檔案是具有 5 個資料行和 150 個資料列的資料表。 它有四個數值功能資料行和一個字串目標資料行。 它沒有資料行標題。

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > 請勿在您的專案資料夾中包含資料檔案，特別是當檔案大小很大時。 我們在此範本中包含 **iris.csv** 以供示範，因為它很小。 如需詳細資訊，請參閱[如何讀取及寫入大型資料檔案](how-to-read-write-files.md)。

2. 在 [資料檢視] 中，按一下加號 (**+**) 來新增新的資料來源。 [新增資料來源] 頁面隨即開啟。 

   ![資料檢視](media/tutorial-classifying-iris/data_view.png)

3. 選取 [文字檔] \(*.csv、.json、.txt 等)，然後按 [下一步]。
   ![資料來源](media/tutorial-classifying-iris/data-source.png)
   

4. 瀏覽至檔案 **iris.csv**，然後按 [下一步]。  
 
   ![選取鳶尾花](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >針對此練習，請確定您從目前的專案目錄內選取 **iris.csv** 檔案。 否則，接下來的步驟可能會失敗。
   
5. 保留預設值，然後按一下 [完成]。

6. 名為 **iris-1.dsource** 的新檔案隨即建立。 由於範例專案中隨附未編號的 **iris.dsource** 檔案，因此會以 "-1" 為檔案指定唯一的名稱。  

   檔案隨即開啟，並且顯示資料。 從 **Column1** 至 **Column5** 的一系列資料行標題，會自動新增到此資料集。 捲動到底部，並注意資料集的最後一個資料列是空的。 資料列是空的，因為 CSV 檔案中有額外的分行符號。

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/iris_data_view.png)

7. 選取 [計量] 按鈕。 觀察長條圖。 每個資料行會計算出一組完整的統計資料。 您也可以選取 [資料] 按鈕再次查看資料。 

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/iris_metrics_view.png)

8. 選取 [準備] 按鈕。 [準備] 對話方塊隨即開啟。 

   範例專案會隨附 **iris.dprep** 檔案。 根據預設，它會要求您在已經存在的 **iris.dprep** 資料準備套件中建立新的資料流。 

   從下拉式功能表中選取 [+ 新增資料準備套件]、輸入新的值作為套件名稱、使用 **iris-1**，然後選取 [確定]。

   ![鳶尾花資料檢視](media/tutorial-classifying-iris/new_dprep.png)

   名為 **iris-1.dprep** 的新資料準備套件隨即建立，並在資料準備編輯器中開啟。

9. 現在讓我們執行一些基本的資料準備動作。 選取各個資料行標頭，讓標頭文字可進行編輯，然後如下所示，將每個資料行重新命名： 

   分別針對五個資料行，依序輸入 [萼片長度]、[萼片寬度]、[花瓣長度]、[花瓣寬度] 和 [物種]。

   ![將資料行重新命名](media/tutorial-classifying-iris/rename_column.png)

10. 若要計算相異值數目，選取 [物種] 資料行，然後按一下滑鼠右鍵來加以選取。 從下拉式功能表中選取 [值計數]。 

   這個動作會開啟資料下方的 [偵測器] 窗格。 此時會出現有四個橫條的長條圖。 目標資料行有三個相異值：**Iris_virginica**、**Iris_versicolor**、**Iris-setosa** 和 **(null)** 值。

   ![選取值計數](media/tutorial-classifying-iris/value_count.png)

11. 若要篩選出 null 值，請選取 [Null] 標籤並選取減號 (**-**)。 接著，Null 資料列會變成灰色，表示它已被篩選掉。 

   ![值計數長條圖](media/tutorial-classifying-iris/filter_out.png)

12. 請注意 [步驟] 窗格中詳述的個別步驟。 當您將資料行重新命名並篩選 null 值資料列時，每個動作都會記錄為資料準備步驟。 您可以編輯個別的步驟，以調整設定、重新排列步驟，並移除步驟。

   ![步驟](media/tutorial-classifying-iris/steps.png)

13. 關閉資料準備編輯器。 在包含圖形圖示的 **iris-1**索引標籤上，選取 [關閉] (x)。 您的工作會自動儲存到顯示在 [資料準備] 標題之下的 **iris-1.dprep** 檔案中。

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>產生 Python/PySpark 程式碼來叫用資料準備套件

<!-- The output/results of a Package can be explored in Python or via a Jupyter Notebook. A Package can be executed across multiple runtimes including local Python, Spark (including in Docker), and HDInsight. A Package contains one or more Dataflows that are the steps and transforms applied to the data. A Package may use another Package as a Data Source (referred to as a Reference Data Flow). -->

1. 在 [資料準備] 索引標籤下尋找 **iris-1.dprep** 檔案。

1. 以滑鼠右鍵按一下 **iris-1.dprep** 檔案，然後從快顯功能表中選取 [產生資料存取碼檔案]。 

   ![產生程式碼](media/tutorial-classifying-iris/generate_code.png)

   名為 **iris-1.py** 的新檔案隨即開啟，其中包含下列幾行程式碼，以便叫用您建立為資料準備套件的邏輯：

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   根據執行此程式碼所在的內容，`df` 代表不同種類的資料框架。 在 Python 執行階段中執行時使用 [pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)，或在 Spark 內容中執行時使用 [Spark 資料框架](https://spark.apache.org/docs/latest/sql-programming-guide.html)。 
   
   如需了解如何在 Azure Machine Learning Workbench 中準備資料，請參閱[開始進行資料準備](data-prep-getting-started.md)指南。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您使用了 Azure Machine Learning Workbench 來：
> [!div class="checklist"]
> * 建立新專案
> * 建立資料準備封裝
> * 產生 Python/PySpark 程式碼來叫用資料準備套件

您已準備好進行本教學課程系列中的下一個部分，將了解如何建置 Azure Machine Learning 模型：
> [!div class="nextstepaction"]
> [教學課程 2 - 建置模型](tutorial-classifying-iris-part-2.md)
