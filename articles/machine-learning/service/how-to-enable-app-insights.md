---
title: Monitor and collect data from Machine Learning web service endpoints
titleSuffix: Azure Machine Learning
description: Monitor web services deployed with Azure Machine Learning using Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 19dba88bf04ee84459ebd9ef0279f125724d7522
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406442"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitor and collect data from ML web service endpoints
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this article, you learn how to collect data from and monitor models deployed to web service endpoints in Azure Kubernetes Service (AKS) or Azure Container Instances (ACI) by enabling Azure Application Insights. In addition to collecting an endpoint's input data and response, you can monitor:

* 要求速率、回應時間和失敗率
* 相依性速率、回應時間和失敗率
* 例外狀況

[Learn more about Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)

* 已安裝 Azure Machine Learning 工作區、包含指令碼的本機目錄，以及適用於 Python 的 Azure Machine Learning SDK。 To learn how to get these prerequisites, see [How to configure a development environment](how-to-configure-environment.md)
* 要部署至 Azure Kubernetes Service (AKS) 或 Azure Container 執行個體 (ACI) 的已訓練機器學習模型。 If you don't have one, see the [Train image classification model](tutorial-train-models-with-aml.md) tutorial

## <a name="web-service-input-and-response-data"></a>Web service input and response data

The input and response to the service - corresponding to the inputs to the ML model and its prediction - are logged to the Azure Application Insights traces under the message `"model_data_collection"`. You can query Azure Application Insights directly to access this data, or set up a [continuous export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) to a storage account for longer retention or further processing. Model data can then be used in the Azure ML service to setup labeling, retraining, explainability, data analysis, or other use. 

## <a name="use-the-azure-portal-to-configure"></a>Use the Azure portal to configure

You can enable and disable Azure Application Insights in the Azure portal. 

1. In the [Azure portal](https://portal.azure.com), open your workspace

1. On the **Deployments** tab, select the service where you want to enable Azure Application Insights

   [![[部署] 索引標籤上的服務清單](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Select **Edit**

   [![[編輯] 按鈕](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. In **Advanced Settings**, select the **Enable AppInsights diagnostics** check box

   [![已選取啟用診斷的核取方塊](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Select **Update** at the bottom of the screen to apply the changes

### <a name="disable"></a>停用

1. In the [Azure portal](https://portal.azure.com), open your workspace
1. Select **Deployments**, select the service, and then select **Edit**

   [![使用 [編輯] 按鈕](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. In **Advanced Settings**, clear the **Enable AppInsights diagnostics** check box

   [![已清除啟用診斷的核取方塊](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Select **Update** at the bottom of the screen to apply the changes
 
## <a name="use-python-sdk-to-configure"></a>Use Python SDK to configure 

### <a name="update-a-deployed-service"></a>更新已部署的服務

1. 識別您工作區中的服務。 The value for `ws` is the name of your workspace

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Update your service and enable Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>記錄您服務中的自訂追蹤

如果您想要記錄自訂追蹤，請依循[部署方式及位置](how-to-deploy-and-where.md)文件中的 AKS 或 ACI 標準部署程序。 然後使用下列步驟：

1. Update the scoring file by adding print statements
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Update the service configuration
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Build an image and deploy it on [AKS](how-to-deploy-to-aks.md) or [ACI](how-to-deploy-to-aci.md)

### <a name="disable-tracking-in-python"></a>在 Python 中停用追蹤

To disable Azure Application Insights, use the following code:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>評估資料
Your service's data is stored in your Azure Application Insights account, within the same resource group as Azure Machine Learning.
若要檢視：

1. Go to your Machine Learning service workspace in [Azure Machine Learning studio](https://ml.azure.com) and click on Application Insights link

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Select the **Overview** tab to see a basic set of metrics for your service

   [![概觀](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. To look into your web service input and response payloads, select **Analytics**
1. In the schema section, select **Traces** and filter down traces with the message `"model_data_collection"`. In the custom dimensions, you can see the inputs, predictions, and other relevant details

   [![Model data](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. To look into your custom traces, select **Analytics**
4. 在 [結構描述] 區段中，選取 [追蹤]。 然後選取 [執行] 來執行查詢。 Data should appear in a table format and should map to your custom calls in your scoring file

   [![自訂追蹤](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

To learn more about how to use Azure Application Insights, see [What is Application Insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Export data for further processing and longer retention

You can use Azure Application Insights' [continuous export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) to send messages to a supported storage account, where a longer retention can be set. The `"model_data_collection"` messages are stored in JSON format and can be easily parsed to extract model data. Azure Data Factory, Azure ML Pipelines, or other data processing tools can be used to transform the data as needed. When you have transformed the data, you can then register it with the Azure Machine Learning workspace as a dataset. To do so, see [How to create and register datasets](how-to-create-register-datasets.md).

   [![Continuous Export](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>範例筆記本

The [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook demonstrates concepts in this article. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>後續步驟

* See [how to deploy a model to an Azure Kubernetes Service cluster](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) or [how to deploy a model to Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) to deploy your models to web service endpoints, and enable Azure Application Insights to leverage data collection and endpoint monitoring
* See [MLOps: Manage, deploy, and monitor models with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) to learn more about leveraging data collected from models in production. Such data can help to continually improve your machine learning process
