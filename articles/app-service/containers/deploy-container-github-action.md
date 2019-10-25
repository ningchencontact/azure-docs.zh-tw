---
title: 使用 GitHub 動作從 CI/CD 管線部署您的容器-Azure App Service |Microsoft Docs
description: 瞭解如何使用 GitHub 動作將您的容器部署至 App Service
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809808"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>部署至用於容器的 Web App 的 GitHub 動作

[GitHub 動作](https://help.github.com/en/articles/about-github-actions)可讓您彈性地建立自動化軟體發展生命週期工作流程。 透過適用于 GitHub 的 Azure App Service 動作，您可以使用 GitHub 動作，將工作流程自動化以部署[適用于容器的 Azure Web Apps](https://azure.microsoft.com/services/app-service/containers/) 。

> [!IMPORTANT]
> GitHub 動作目前為搶鮮版（Beta）。 您必須先[註冊，才能](https://github.com/features/actions)使用您的 GitHub 帳戶加入預覽版。
> 

工作流程是由存放庫中 `/.github/workflows/` 路徑中的 YAML （. yml）檔案所定義。 此定義包含組成工作流程的各種步驟和參數。

針對 Azure Web 應用程式容器工作流程，檔案有三個區段：

|區段  |工作  |
|---------|---------|
|**驗證** | 1. 定義服務主體 <br /> 2. 建立 GitHub 秘密 |
|**建置** | 1. 設定環境 <br /> 2. 建立容器映射 |
|**部署** | 1. 部署容器映射 |

## <a name="create-a-service-principal"></a>建立服務主體

您可以使用[Azure CLI](https://docs.microsoft.com/cli/azure/)中的[az ad sp create-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令來建立[服務主體](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 您可以使用 Azure 入口網站中的[Azure Cloud Shell](https://shell.azure.com/)或選取 [**試試看**] 按鈕來執行此命令。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

在此範例中，將資源中的預留位置取代為您的訂用帳戶識別碼、資源群組和 web 應用程式名稱。 輸出是可提供 Web 應用程式存取權的角色指派認證。 複製這個 JSON 物件，您可以使用它從 GitHub 進行驗證。

> [!NOTE]
> 如果您決定使用發行設定檔進行驗證，則不需要建立服務主體。

> [!IMPORTANT]
> 授與最小存取權一律是最佳作法。 這就是上一個範例中的範圍僅限於特定 web 應用程式，而不是整個資源群組的原因。

## <a name="configure-the-github-secret"></a>設定 GitHub 秘密

下列範例會使用使用者層級的認證，例如用於部署的 Azure 服務主體。 請遵循下列步驟來設定密碼：

1. 在[GitHub](https://github.com/)中，流覽您的存放庫、選取 [**設定] > 秘密 > 新增新密碼**

2. 將下列 `az cli` 命令的內容貼到 [秘密變數] 的值。 例如： `AZURE_CREDENTIALS` 。

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. 現在，在您分支中的工作流程檔案中： `.github/workflows/workflow.yml` 以您的密碼取代 Azure 登入動作中的密碼。

4. 同樣地，針對容器登錄認證定義下列額外的秘密，並在 Docker 登入動作中加以設定。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. 定義之後，您將會看到如下所示的秘密。

    ![容器秘密](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>建立容器映射

下列範例顯示建立 docker 映射的工作流程的一部分。

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-web-app-container"></a>部署至 Web 應用程式容器

若要將您的映射部署至 web 應用程式容器，您必須使用 `Azure/appservice-actions/webapp@master` 動作。 此動作有5個參數：

| **參數**  | **說明**  |
|---------|---------|
| **應用程式名稱** | 具備Azure Web 應用程式的名稱 | 
| **位置名稱** | 選擇性輸入生產位置以外的現有插槽 |
| **images** | 具備指定完整的容器映射名稱。 例如，' myregistry.azurecr.io/nginx:latest ' 或 ' python： 3.7.2-alpine/'。 針對多容器案例，可以提供多個容器映射名稱（以多行分隔） |
| **設定檔** | 選擇性Docker 撰寫檔案的路徑。 應該是完整路徑或相對於預設工作目錄。 多容器案例所需 |
| **容器-命令** | 選擇性輸入 [啟動] 命令。 針對 ex dotnet run 或 dotnet filename .dll |

以下是建立 node.js Web 應用程式並將其部署至 Azure Web 應用程式容器的範例工作流程。

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>後續步驟

您可以在 GitHub 上找到分組到不同存放庫的一組動作，其中每一個都包含檔和範例，以協助您使用 GitHub 來進行 CI/CD，並將您的應用程式部署至 Azure。

- [Azure 登入](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [適用于容器的 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 登入/登出](https://github.com/Azure/docker-login)

- [觸發工作流程的事件](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 部署](https://github.com/Azure/k8s-deploy)

- [入門工作流程](https://github.com/actions/starter-workflows)
