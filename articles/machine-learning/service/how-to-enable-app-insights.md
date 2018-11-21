---
title: 在生產環境中啟用 Azure Machine Learning Services 的 Application Insights
description: 了解如何設定 Application Insights 以便讓 Azure Machine Learning 服務部署至 Azure Kubernetes Service
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 285486d5fe641d49ee21d7340b62f83d75862553
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578292"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>使用 Application Insights 監視您生產環境中的 Azure Machine Learning 模型

在本文中，您會了解如何針對 Azure Machine Learning 服務設定 Azure Application Insights。 Application Insights 可讓您監視：
* 要求速率、回應時間和失敗率。
* 相依性速率、回應時間和失敗率。
* 例外狀況。

[深入了解 Application Insights](../../application-insights/app-insights-overview.md)。 

>[!NOTE]
> 本文中的程式碼使用 Azure Machine Learning SDK 0.1.74 版進行測試


## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 已安裝 Azure Machine Learning 工作區、包含指令碼的本機目錄，以及適用於 Python 的 Azure Machine Learning SDK。 若要了解如何取得這些必要條件，請參閱[如何設定開發環境](how-to-configure-environment.md)。
* 要部署至 Azure Kubernetes Service (AKS) 的訓練過機器學習模型。 如果您沒有模型，請參閱[將影像分類模型定型](tutorial-train-models-with-aml.md)教學課程。
* [AKS 叢集](how-to-deploy-to-aks.md)。

## <a name="enable-and-disable-in-the-portal"></a>在入口網站中啟用及停用

您可以在 Azure 入口網站中啟用及停用 Application Insights。

### <a name="enable"></a>啟用

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

   [![[編輯] 按鈕](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. 在 [進階設定] 中，清除 [啟用 AppInsights 診斷] 核取選項。 

   [![已清除啟用診斷的核取方塊](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 選取螢幕底部的 [更新] 以套用變更。 

## <a name="enable-and-disable-from-the-sdk"></a>從 SDK 啟用及停用

### <a name="update-a-deployed-service"></a>更新已部署的服務
1. 識別您工作區中的服務。 `ws` 的值是工作區的名稱。

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新您的服務，並啟用 Application Insights。 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>記錄您服務中的自訂追蹤
如果您想要記錄自訂追蹤，請遵循 [AKS 的標準部署程序](how-to-deploy-to-aks.md)。 然後：

1. 新增 print 陳述式以更新評分檔案。
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新 AKS 組態。
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [建置映像並加以部署](how-to-deploy-to-aks.md)。  

### <a name="disable-tracking-in-python"></a>在 Python 中停用追蹤

若要停用 Application Insights，請使用下列程式碼：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

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

若要深入了解如何使用 Application Insights，請參閱[什麼是 Application Insights？](../../application-insights/app-insights-overview.md)。
    

## <a name="example-notebook"></a>範例筆記本

[00.Getting Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) Notebook 會示範本文中的概念。  請取得此筆記本：
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟
您也可以在生產環境中收集您模型上的資料。 閱讀[在生產環境中收集模型資料](how-to-enable-data-collection.md)一文。 


## <a name="other-references"></a>其他參考資料
* [適用於容器的 Azure 監視器](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)
