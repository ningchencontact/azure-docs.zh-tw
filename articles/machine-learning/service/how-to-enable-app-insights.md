---
title: 在生產環境中啟用 Azure Machine Learning Services 的 Application Insights
description: 了解如何為 Azure Kubernetes Service 中部署的 Azure Machine Learning 服務設定 Application Insights
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114561"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>使用 Application Insights 監視您生產環境中的 Azure Machine Learning 模型

在本文中，您可以了解如何針對您的 **Azure Machine Learning** 服務設定 **Application Insights**。 一旦啟用後，Application Insights 可讓您監視：
* 要求速率、回應時間和失敗率
* 相依性速率、回應時間和失敗率
* 例外狀況

在[這裡](../../application-insights/app-insights-overview.md)深入了解 Application Insights。 

## <a name="prerequisites"></a>必要條件
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 已安裝 Azure Machine Learning 工作區、包含您指令碼的本機目錄，以及適用於 Python 的 Azure Machine Learning SDK。 了解如何使用[如何設定開發環境](how-to-configure-environment.md)文件來取得這些必要條件。
* 要部署至 Azure Kubernetes Service (AKS) 的訓練過機器學習模型。 如果您沒有該模型，請參閱[訓練影像分類模型](tutorial-train-models-with-aml.md)教學課程。
* [AKS 叢集](how-to-deploy-to-aks.md)。

## <a name="enable--disable-in-the-portal"></a>在入口網站中啟用及停用

您可以在 Azure 入口網站中啟用及停用 Application Insights。

### <a name="enable"></a>啟用

1. 在 [Azure 入口網站](https://portal.azure.com)中開啟工作區。

1. 移至您的部署，並選取您要在其中啟用 Application Insights 的服務。

   [![部署](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. 按一下 [編輯] 並移至 [進階設定]。

   [![編輯](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. 在 [進階設定] 中，選取 [啟用 Application Insights 診斷]。

   [![編輯](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. 選取螢幕底部的 [更新] 以套用變更。 

### <a name="disable"></a>停用
若要在入口網站中停用 Application Insights，請執行下列步驟：

1. 登入 Azure 入口網站，網址為： https://portal.azure.com。
1. 移至工作區。
1. 依序選擇 [部署]、[選取服務] 和 [編輯]。

   [![編輯](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. 在 [進階設定] 中，取消選取 [啟用 AppInsights 診斷] 選項。 

   [![取消選取](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. 選取螢幕底部的 [更新] 以套用變更。 

## <a name="enable--disable-from-the-sdk"></a>從 SDK 啟用及停用

### <a name="update-a-deployed-service"></a>更新已部署的服務
1. 識別您工作區中的服務 (ws = 您工作區中的名稱)

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. 更新您的服務，並啟用 Application Insights。 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>記錄您服務中的自訂追蹤
如果您想要記錄自訂追蹤，您將要遵循 [AKS 的標準部署程序](how-to-deploy-to-aks.md)，而且您將：

1. 新增 print 陳述式以更新評分檔案。
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. 更新 AKS 組態。
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [建置映像並加以部署。](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>在 Python 中停用追蹤

若要停用 Application Insights，請使用下列程式碼：

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>評估資料
您服務的資料會儲存在您的 Application Insights 帳戶中，這個 Application Insights 帳戶與 Azure Machine Learning Services 工作區位於相同的資源群組內。
若要檢視：
1. 前往 [Azure 入口網站](https://portal.azure.com)中的資源群組，然後按一下進入 Application Insights 資源。 
2. [概觀] 索引標籤會顯示一組您服務的基本計量。

   [![概觀](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. 若要查看您的自訂追蹤，請按一下 [Analytics]。
4. 在結構描述區段內，按一下 [追蹤]，然後 [執行] 您的查詢。 資料應該會以下方資料表格式出現，且應該會對應至您評分檔案中的自訂呼叫。 

   [![自訂追蹤](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

若要深入了解如何使用 Application Insights，按一下[此處](../../application-insights/app-insights-overview.md)。
    

## <a name="example-notebook"></a>範例 Notebook

[00.Getting Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) Notebook 會示範本文中的概念。  請取得此筆記本：
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟
您也可以在生產環境中收集您模型上的資料。 閱讀[在生產環境中收集模型資料](how-to-enable-data-collection.md)的文章 
