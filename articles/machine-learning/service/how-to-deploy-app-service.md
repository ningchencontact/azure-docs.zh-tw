---
title: 將 ml 模型部署至 Azure App Service (預覽)
titleSuffix: Azure Machine Learning service
description: 瞭解如何使用 Azure Machine Learning 服務, 在 Azure App Service 中將模型部署至 Web 應用程式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: a5fd376a6da70ed68baedf44fd4c2cc47e68d3cf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872379"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>將機器學習模型部署到 Azure App Service (預覽)

瞭解如何在 Azure App Service 中將模型從 Azure Machine Learning 服務部署為 web 應用程式。

> [!IMPORTANT]
> 雖然 Azure Machine Learning 服務和 Azure App Service 都已正式推出, 但從 Machine Learning 服務將模型部署至 App Service 的功能仍處於預覽狀態。

有了 Azure Machine Learning 服務, 您就可以從定型的機器學習模型建立 Docker 映射。 此映射包含接收資料、將其提交至模型, 然後傳迴響應的 web 服務。 Azure App Service 可以用來部署映射, 並提供下列功能:

* 增強式安全性的先進[驗證](/azure/app-service/configure-authentication-provider-aad)。 驗證方法包括 Azure Active Directory 和多重要素驗證。
* [自動](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)調整, 而不需要重新部署。
* 用戶端與服務之間安全通訊的[SSL 支援](/azure/app-service/app-service-web-ssl-cert-load)。

如需 Azure App Service 所提供之功能的詳細資訊, 請參閱[App Service 總覽](/azure/app-service/overview)。

> [!IMPORTANT]
> 如果您需要記錄已部署模型所使用之計分資料的功能, 或評分的結果, 您應該改為部署到 Azure Kubernetes Service。 如需詳細資訊, 請參閱[收集生產環境模型的資料](how-to-enable-data-collection.md)。

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 服務工作區。 如需詳細資訊, 請參閱[建立工作區](how-to-manage-workspace.md)文章。
* 在您的工作區中註冊的定型機器學習模型。 如果您沒有模型, 請使用[影像分類教學課程: 訓練模型](tutorial-train-models-with-aml.md)來定型並註冊一個。

    > [!IMPORTANT]
    > 本文中的程式碼片段假設您已設定下列變數:
    >
    > * `ws`-您的 Azure Machine Learning 工作區。
    > * `model`-即將部署的已註冊模型。
    > * `inference_config`-模型的推斷設定。
    >
    > 如需有關設定這些變數的詳細資訊, 請參閱[使用 Azure Machine Learning 服務來部署模型](how-to-deploy-and-where.md)。

## <a name="prepare-for-deployment"></a>準備部署

在部署之前, 您必須定義以 web 服務的形式執行模型所需的內容。 下列清單描述部署所需的基本專案:

* __輸入腳本__。 此腳本會接受要求、使用模型對要求評分, 並傳回結果。

    > [!IMPORTANT]
    > 專案腳本專屬於您的模型;它必須瞭解傳入要求資料的格式、您的模型所預期的資料格式, 以及傳回給用戶端的資料格式。
    >
    > 如果要求資料的格式無法供您的模型使用, 腳本就可以將它轉換成可接受的格式。 它也可以在將回應傳回給用戶端之前, 先將它轉換。

    > [!IMPORTANT]
    > Azure Machine Learning SDK 不會提供 web 服務存取資料存放區或資料集的方式。 如果您需要部署的模型來存取儲存在部署外部的資料, 例如在 Azure 儲存體帳戶中, 您必須使用相關的 SDK 開發自訂程式碼解決方案。 例如,[適用于 Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)。
    >
    > 另一個適用于您案例的替代方法是[批次預測](how-to-run-batch-predictions.md), 這可在計分時提供對資料存放區的存取。

    如需有關輸入腳本的詳細資訊, 請參閱[使用 Azure Machine Learning 服務來部署模型](how-to-deploy-and-where.md)。

* 執行專案腳本或模型所需的相依性, 例如 helper 腳本或 Python/Conda 套件

這些實體會封裝成__推斷__設定。 推斷設定會參考專案腳本和其他相依性。

> [!IMPORTANT]
> 建立要與 Azure App Service 搭配使用的推斷設定時, 您必須使用[環境](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py)物件。 下列範例示範如何建立環境物件, 並將它與推斷設定搭配使用:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

如需環境的詳細資訊, 請參閱[建立和管理用於定型和部署的環境](how-to-use-environments.md)。

如需有關推斷設定的詳細資訊, 請參閱[使用 Azure Machine Learning 服務來部署模型](how-to-deploy-and-where.md)。

> [!IMPORTANT]
> 部署到 Azure App Service 時, 您不需要建立__部署__設定。

## <a name="create-the-image"></a>建立映像

若要建立部署到 Azure App Service 的 Docker 映射, 請使用 [ [Model. package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-)]。 下列程式碼片段示範如何從模型和推斷設定建立新的影像:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

若`show_output=True`為, 則會顯示 Docker 組建進程的輸出。 程式完成後, 就會在您工作區的 Azure Container Registry 中建立映射。

## <a name="deploy-image-as-a-web-app"></a>將映射部署為 web 應用程式

1. 從  [Azure 入口網站](https://portal.azure.com)中, 選取您的 Azure Machine Learning 工作區。 在 [__總覽__] 區段中,使用 [登錄] 連結來存取工作區的 Azure Container Registry。

    ![工作區總覽的螢幕擷取畫面](media/how-to-deploy-app-service/workspace-overview.png)

2. 從 [Azure Container Registry] 中, 選取 [__存放庫__], 然後選取您要部署的__映射名稱__。 針對您想要部署的版本, 選取 [ __...__ ] 專案, 然後 [__部署至 web 應用程式__]。

    ![從 ACR 部署至 web 應用程式的螢幕擷取畫面](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. 若要建立 Web 應用程式, 請提供網站名稱、訂用帳戶、資源群組, 然後選取 [App service 方案/位置]。 最後，選取 [建立]。

    ![[新增 web 應用程式] 對話方塊的螢幕擷取畫面](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>使用 Web 應用程式

從 [ [Azure 入口網站](https://portal.azure.com)] 中, 選取在上一個步驟中建立的 Web 應用程式。 在 [__總覽__] 區段中, 複製 [ __URL__]。 此值是服務的__基底 URL__ 。

![Web 應用程式總覽的螢幕擷取畫面](media/how-to-deploy-app-service/web-app-overview.png)

將要求傳遞至模型的 web 服務位於`{baseurl}/score`。 例如： `https://mywebapp.azurewebsites.net/score` 。 下列 Python 程式碼示範如何將資料提交至 URL, 並顯示回應:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>後續步驟

* 如需設定 Web 應用程式的詳細資訊, 請參閱[Linux 上的 App Service](/azure/app-service/containers/)檔。
* 如需有關調整的詳細資訊, 請參閱[開始使用 Azure 中的自動](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)調整。
* 如需 SSL 支援的詳細資訊, 請參閱[在您的 Azure App Service 中使用 ssl 憑證](/azure/app-service/app-service-web-ssl-cert-load)。
* 如需驗證的詳細資訊, 請參閱[設定您的 App Service 應用程式以使用 Azure Active Directory 登入](/azure/app-service/configure-authentication-provider-aad)。
* [取用部署為 Web 服務的 ML 模型](how-to-consume-web-service.md)