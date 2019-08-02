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
ms.openlocfilehash: 11e8b26c81d3a8f672abbeafc153df73d063cb6e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612232"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>將機器學習模型部署到 Azure App Service (預覽)

瞭解如何在 Azure App Service 中將模型從 Azure Machine Learning 服務部署為 web 應用程式。

> [!IMPORTANT]
> 雖然 Azure Machine Learning 服務和 Azure App Service 都已正式推出, 但從 Machine Learning 服務將模型部署至 App Service 的功能仍處於預覽狀態。

有了 Azure Machine Learning 服務, 您就可以從定型的機器學習模型建立 Docker 映射。 此映射包含接收資料、將其提交至模型, 然後傳迴響應的 web 服務。 Azure App Service 可以用來部署映射, 並提供下列功能:

* 用戶端與服務之間安全通訊的[SSL 支援](/azure/app-service/app-service-web-ssl-cert-load)。
* [相應](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)放大至多個實例, 而不需要重新部署。
* 增強式安全性的[先進驗證](/azure/app-service/configure-authentication-provider-aad)。

如需 Azure App Service 所提供之功能的詳細資訊, 請參閱[App Service 總覽](/azure/app-service/overview)。

## <a name="prerequisites"></a>先決條件

* Azure Machine Learning 服務工作區。 如需詳細資訊, 請參閱[建立工作區](setup-create-workspace.md)文章。
* 在您的工作區中註冊的定型機器學習模型。 如果您沒有模型, 請使用[影像分類教學課程: 訓練模型](tutorial-train-models-with-aml.md)來定型並註冊一個。
* 從模型建立的 Docker 映射。 如果您沒有影像, 請使用 [[映射分類: 部署模型](tutorial-deploy-models-with-aml.md)] 來建立映射。

## <a name="deploy-image-as-a-web-app"></a>將映射部署為 Web 應用程式

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

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

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