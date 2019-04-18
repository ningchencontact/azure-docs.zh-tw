---
title: 設定 Azure Application Insights 來監視 ML 模型
titleSuffix: Azure Machine Learning service
description: 監視部署與使用 Azure Application Insights 的 Azure Machine Learning 服務的 web 服務
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 2e481a388d8cbd6baf66b95c74449396b2e70f7d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885486"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>使用 Application Insights 監視您的 Azure Machine Learning 模型

在本文中，您會了解如何針對 Azure Machine Learning 服務設定 Azure Application Insights。 Application Insights 可讓您監視：
* 要求速率、回應時間和失敗率。
* 相依性速率、回應時間和失敗率。
* 例外狀況。

[深入了解 Application Insights](../../azure-monitor/app/app-insights-overview.md)。 


## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning 服務](https://aka.ms/AMLFree)。

* 已安裝 Azure Machine Learning 工作區、包含指令碼的本機目錄，以及適用於 Python 的 Azure Machine Learning SDK。 若要了解如何取得這些必要條件，請參閱[如何設定開發環境](how-to-configure-environment.md)。
* 要部署至 Azure Kubernetes Service (AKS) 或 Azure Container 執行個體 (ACI) 的已訓練機器學習模型。 如果您沒有模型，請參閱[將影像分類模型定型](tutorial-train-models-with-aml.md)教學課程。


## <a name="use-sdk-to-configure"></a>使用 SDK 設定 

### <a name="update-a-deployed-service"></a>更新已部署的服務
1. 識別您工作區中的服務。 `ws` 的值是工作區的名稱。

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新您的服務，並啟用 Application Insights。 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>記錄您服務中的自訂追蹤
如果您想要記錄自訂追蹤，請依循[部署方式及位置](how-to-deploy-and-where.md)文件中的 AKS 或 ACI 標準部署程序。 然後使用下列步驟：

1. 新增 print 陳述式以更新評分檔案。
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新服務組態。
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. 建置映像並將其部署在 [AKS](how-to-deploy-to-aks.md) 或 [ACI](how-to-deploy-to-aci.md) 上。  

### <a name="disable-tracking-in-python"></a>在 Python 中停用追蹤

若要停用 Application Insights，請使用下列程式碼：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>使用入口網站設定

您可以在 Azure 入口網站中啟用及停用 Application Insights。

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的工作區。

1. 在 [部署] 索引標籤上，選取您要在其中啟用 Application Insights 的服務。

   [![[部署] 索引標籤上的服務清單](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. 選取 [編輯]。

   [![[編輯] 按鈕](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. 在 [進階設定] 中，選取 [啟用 AppInsights 診斷] 核取選項。

   [![已選取啟用診斷的核取方塊](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 選取螢幕底部的 [更新] 以套用變更。 

### <a name="disable"></a>停用
1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您的工作區。
1. 選取 [部署]，選取服務，然後選取 [編輯]。

   [![使用 [編輯] 按鈕](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. 在 [進階設定] 中，清除 [啟用 AppInsights 診斷] 核取選項。 

   [![已清除啟用診斷的核取方塊](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 選取螢幕底部的 [更新] 以套用變更。 
 

## <a name="evaluate-data"></a>評估資料
服務資料會儲存在您的 Application Insights 帳戶中，這個帳戶位於與 Azure Machine Learning 服務相同的資源群組內。
若要檢視：
1. 移至 [Azure 入口網站](https://portal.azure.com)中的 Machine Learning 服務工作區，然後按一下 Application Insights 連結。

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. 選取 [概觀] 索引標籤以查看一組您服務的基本計量。

   [![概觀](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. 若要查看您的自訂追蹤，請選取 [Analytics]。
4. 在 [結構描述] 區段中，選取 [追蹤]。 然後選取 [執行] 來執行查詢。 資料應該會以資料表格式出現，且應該會對應至您評分檔案中的自訂呼叫。 

   [![自訂追蹤](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

若要深入了解如何使用 Application Insights，請參閱[什麼是 Application Insights？](../../azure-monitor/app/app-insights-overview.md)。
    

## <a name="example-notebook"></a>範例筆記本

[how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) Notebook 會示範本文中的概念。 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟
您也可以在生產環境中收集您模型上的資料。 閱讀[在生產環境中收集模型資料](how-to-enable-data-collection.md)一文。 

另請閱讀[適用於容器的 Azure 監視器](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)。
