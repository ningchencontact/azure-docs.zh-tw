---
title: 收集生產環境模型的相關資料
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Blob 儲存體中收集 Azure Machine Learning 輸入模型資料。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: a127a211157edb0b26d0495bc2ed05dd79323111
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842629"
---
# <a name="collect-data-for-models-in-production"></a>在生產環境中收集模型資料

在本文中，您可以了解如何將輸入模型資料從您已部署至 Azure Kubernetes Cluster (AKS) 的 Azure Machine Learning 服務收集到 Azure Blob 儲存體。 

啟用之後，您收集的此資料可協助您：
* 監視生產資料進入您模型時的資料變化

* 制定何時重新訓練或最佳化您模型的更佳決策

* 使用收集到的資料重新訓練模型

## <a name="what-is-collected-and-where-does-it-go"></a>收集的內容和其放置位置？

下列是可收集的資料：
* 來自 Azure Kubernetes Cluster (AKS) 中所部署 Web 服務的模型**輸入**資料 (**不會**收集語音、影像和影片) 
  
* 使用生產輸入資料的模型預測。

> [!Note]
> 對此資料的預先彙總或預先計算，目前不屬於服務的一部分。   

輸出會儲存至 Azure Blob。 因為資料會新增至 Azure Blob，所以您接著可以選擇慣用的工具來執行分析。 

Blob 中輸出資料的路徑遵循此語法：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

- 已安裝 Machine Learning services 工作區、包含您指令碼的本機目錄，以及適用於 Python 的 Azure Machine Learning SDK。 了解如何使用[如何設定開發環境](how-to-configure-environment.md)文件來取得這些必要條件。

- 要部署至 Azure Kubernetes Service (AKS) 的訓練過機器學習模型。 如果您沒有模型，請參閱[將影像分類模型定型](tutorial-train-models-with-aml.md)教學課程。

- Azure Kubernetes Service 叢集。 如需了解如何建立一個叢集並部署至該叢集，請參閱[部署方式及位置](how-to-deploy-and-where.md)文件。

- [設定您的環境](how-to-configure-environment.md)並安裝[監視 SDK](https://aka.ms/aml-monitoring-sdk)。

## <a name="enable-data-collection"></a>啟用資料收集
不論透過 Azure Machine Learning Service 或其他工具所部署的模型為何，都可以啟用資料收集。 

若要予以啟用，您需要：

1. 開啟評分檔案。 

1. 在檔案開頭處新增[下列程式碼](https://aka.ms/aml-monitoring-sdk)：

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. 在 `init()` 函式中宣告資料收集變數：

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    CorrelationId 是選用參數，如果您的模型不需要它，則不需要設定。 備妥 correlationId 可協助您更輕鬆地對應其他資料  (範例包括：LoanNumber、CustomerId 等)。
    
    Identifier 稍後用於建置 Blob 中的資料夾結構，可用來劃分「原始」資料與「已處理」。

3.  將下列程式碼行新增至 `run(input_df)` 函式：

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. 當您在 AKS 中部署服務而必須更新設定檔案時，資料收集**不會**自動設定為 **true**，例如： 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    透過變更此設定，也可以開啟用於監視服務的 AppInsights：
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. 若要建立一個新映像並部署服務，請參閱[部署方式及位置](how-to-deploy-and-where.md)文件。


如果您的現有服務已在**環境檔案**和**評分檔案**中安裝相依性，則請透過下列方式啟用資料收集：

1. 移至 [Azure 入口網站 ](https://portal.azure.com)。

1. 開啟工作區。

1. 移至 [部署] -> [選取服務] -> [編輯]。

   ![編輯服務](media/how-to-enable-data-collection/EditService.PNG)

1. 在 [進階設定] 中，取消選取 [啟用模型資料收集]。 

    [![核取資料收集](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   在此視窗中，您也可以選擇 [啟用 AppInsights 診斷] 來追蹤您服務的健康狀態。  

1. 選取 [更新] 以套用變更。


## <a name="disable-data-collection"></a>停用資料收集
您隨時可以停止收集資料。 使用 Python 程式碼或 Azure 入口網站來停用資料收集。

+ 選項 1 - 在 Azure 入口網站中停用： 
  1. 登入 [Azure 入口網站](https://portal.azure.com)。

  1. 開啟工作區。

  1. 移至 [部署] -> [選取服務] -> [編輯]。

     [![[編輯] 選項](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. 在 [進階設定] 中，取消選取 [啟用模型資料收集]。 

     [![取消核取資料收集](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. 選取 [更新] 以套用變更。

+ 選項 2 - 使用 Python 停用資料收集：

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>驗證您的資料並加以分析
您可以選擇慣用的工具來分析收集到 Azure Blob 中的資料。 

若要從 Blob 快速存取資料：
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 開啟工作區。
1. 按一下 [儲存體] 。

    [![儲存體](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. 使用此語法遵循 Blob 中輸出資料的路徑：

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>透過 Power BI 分析模型資料

1. 下載並開啟 [PowerBi 桌面](https://www.powerbi.com)

1. 選取 [取得資料]，然後按一下 [[Azure Blob 儲存體]](https://docs.microsoft.com/power-bi/desktop-data-sources)。

    [![PBI Blob 設定](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. 新增您的儲存體帳戶名稱並輸入您的儲存體金鑰。 您可以在 Blob 的 [設定] > [存取金鑰] 中找到這項資訊。 

1. 選取容器 **modeldata**，然後按一下 [編輯]。 

    [![PBI 導覽器](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 在查詢編輯器中，在 [名稱] 資料行下方按一下，新增您的儲存體帳戶 1。 塑造篩選條件的路徑。 注意：如果您只想查看特定年份或月份的檔案，只要展開篩選路徑即可。 例如，只查看年 3 月份的資料：/modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/identifier>/year>/3

1. 根據 [名稱] 篩選與您相關的資料。 如果您儲存了**預測**和**輸入**，則必須建立每一項的查詢。

1. 按一下 [內容] 資料行旁邊的雙箭頭來合併檔案。 

    [![PBI 內容](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. 按一下 [確定]，將會預先載入資料。

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. 您現在可以按一下 [關閉並套用]。

1.  如果您新增了輸入和預測，您的資料表就會自動以 **RequestId** 相互關聯。

1. 開始建置模型資料的自訂報告。


### <a name="analyzing-model-data-using-databricks"></a>使用 Databricks 分析模型資料

1. 建立 [Databricks 工作區](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 

1. 移至 Databricks 工作區。 

1. 在 Databricks 工作區中，選取 [上傳資料]。

    [![BD 上傳](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 建立新資料表，然後選取 [其他資料來源] -> [Azure Blob 儲存體] -> [在 Notebook 中建立資料表]。

    [![DB 資料表](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 更新您的資料位置。 下列是一個範例：

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 請遵循範本上的步驟，以便檢視及分析您的資料。 

## <a name="example-notebook"></a>範例筆記本

[how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) Notebook 會示範本文中的概念。  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
