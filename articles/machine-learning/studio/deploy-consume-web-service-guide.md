---
title: 部署和取用
titleSuffix: ML Studio (classic) - Azure
description: You can use Azure Machine Learning Studio (classic) to deploy machine learning workflows and models as web services. 然後便可以透過網際網路利用這些 Web 服務從應用程式呼叫機器學習服務模型，進行即時預測或批次模式的預測。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 1c97fd4f4d5646b6654f5261abd99372c521c389
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228244"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (classic) Web Services: Deployment and consumption

You can use Azure Machine Learning Studio (classic) to deploy machine learning workflows and models as web services. 然後便可以透過網際網路利用這些 Web 服務從應用程式呼叫機器學習服務模型，進行即時預測或批次模式的預測。 由於 Web 服務為 RESTful，您可以從各種程式設計語言與平台 (如 .NET 與 Java) 和應用程式 (如 Excel) 呼叫它們。

下列章節提供可協助您快速開始的逐步解說、程式碼及文件的連結。

## <a name="deploy-a-web-service"></a>部署 Web 服務

### <a name="with-azure-machine-learning-studio-classic"></a>With Azure Machine Learning Studio (classic)

The Studio (classic) portal and the Microsoft Azure Machine Learning Web Services portal help you deploy and manage a web service without writing code.

下列連結提供有關如何部署新的 Web 服務的一般資訊︰

* 如需如何部署以 Azure Resource Manager 為基礎的新 Web 服務的概觀，請參閱 [部署新的 Web 服務](deploy-a-machine-learning-web-service.md)。
* 如需如何部署 Web 服務的逐步解說，請參閱 [部署 Azure Machine Learning Web 服務](deploy-a-machine-learning-web-service.md)。
* For a full walkthrough about how to create and deploy a web service, start with [Tutorial 1: Predict credit risk](tutorial-part1-credit-risk.md).
* 如需部署 Web 服務的特定範例，請參閱︰

  * [Tutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md)
  * [如何將 Web 服務部署到多個區域](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>使用 Web 服務資源提供者 API (Azure Resource Manager API)

The classic version of Azure Machine Learning Studio resource provider for web services enables deployment and management of web services by using REST API calls. 如需詳細資訊，請參閱 [Machine Learning Web 服務 (REST)](/rest/api/machinelearning/index) 參考資料。

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>使用 PowerShell Cmdlet

The classic version of Azure Machine Learning Studio resource provider for web services enables deployment and management of web services by using PowerShell cmdlets.

To use the cmdlets, you must first sign in to your Azure account from within the PowerShell environment by using the [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet. 如果您不熟悉如何呼叫以 Resource Manager 為基礎的 PowerShell 命令，請參閱 [搭配使用 Azure PowerShell 與 Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md)。

若要匯出預測實驗，請使用這個 [範例程式碼](https://github.com/ritwik20/AzureML-WebServices)。 由程式碼建立 .exe 檔案之後，您可以輸入︰

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

執行應用程式會建立 Web 服務的 JSON 範本。 若要使用此範本部署 Web 服務，必須新增下列資訊︰

* 儲存體帳戶名稱和金鑰

    您可以從 [Azure 入口網站](https://portal.azure.com/)取得儲存體帳戶名稱和金鑰。
* 承諾用量方案識別碼

    您可以從 [Azure Machine Learning Web 服務](https://services.azureml.net) 入口網站登入，按一下方案名稱取得方案識別碼。

將它們新增至 JSON 範本做為 *Properties* 節點的子系，與 *MachineLearningWorkspace* 節點位於相同層級。

以下是範例：

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

如需更詳細的資訊，請參閱下列文章和範例程式碼：

* [Azure Machine Learning Studio (classic) Cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) reference on MSDN
* GitHub 上的範例 [逐步解說](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)

## <a name="consume-the-web-services"></a>取用 Web 服務

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>從 Azure Machine Learning Web Services UI (測試)

您可以從 Azure Machine Learning Web Services 入口網站測試您的 Web 服務。 這包括測試要求-回應服務 (RRS) 和批次執行服務 (BES) 介面。

* [部署新的 Web 服務](deploy-a-machine-learning-web-service.md)
* [部署 Azure Machine Learning Web 服務](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>從 Excel

您可以下載可取用 Web 服務的 Excel 範本︰

* [從 Excel 使用 Azure Machine Learning Web 服務](consuming-from-excel.md)
* [適用於 Azure Machine Learning Web 服務的 Excel 增益集](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>從以 REST 為基礎的用戶端

Azure Machine Learning Web 服務是 RESTful API。 You can consume these APIs from various platforms, such as .NET, Python, R, Java, etc. The **Consume** page for your web service on the [Microsoft Azure Machine Learning Web Services portal](https://services.azureml.net) has sample code that can help you get started. 如需詳細資訊，請參閱 [如何取用 Azure Machine Learning Web 服務](consume-web-services.md)。
