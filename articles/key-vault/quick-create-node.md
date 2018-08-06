---
title: 設定 Azure Web 應用程式以從 Key Vault 讀取密碼的教學課程 | Microsoft Docs
description: 教學課程：設定 Node.js 應用程式，以從 Key Vault 讀取祕密
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 08/01/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: cc43081463667eba06af6538f3d78f16544ed2a5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412237"
---
# <a name="quickstart-how-to-set-and-read-a-secret-from-key-vault-in-a-node-web-app"></a>快速入門：如何在 Node Web 應用程式中進行設定並從 Key Vault 讀取祕密 

本快速入門示範如何將祕密儲存在 Key Vault 中，以及如何使用 Web 應用程式加以擷取。 此 Web 應用程式可以在本機或在 Azure 中執行。 本快速入門會使用 Node.js 和受控服務識別 (MSI)

> [!div class="checklist"]
> * 建立 Key Vault。
> * 將密碼儲存在 Key Vault 中。
> * 從 Key Vault 擷取祕密。
> * 建立 Azure Web 應用程式。
> * [啟用受控服務識別](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)。
> * 授與 Web 應用程式從 Key Vault 讀取資料所需的權限。

繼續之前，請先確定您已熟悉[基本概念](key-vault-whatis.md#basic-concepts)。

## <a name="prerequisites"></a>必要條件

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 或更新版本
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="login-to-azure"></a>登入 Azure

若要使用 CLI 登入 Azure，您可以輸入：

```azurecli
az login
```

## <a name="create-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az_group_create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

請選取資源群組名稱，並填入預留位置。
下列範例會在 eastus 位置建立名為 <YourResourceGroupName> 的資源群組。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

本教學課程中會使用您該建立的資源群組。

## <a name="create-an-azure-key-vault"></a>建立 Azure Key Vault

接下來，您會使用在上一個步驟中建立的資源群組建立 Key Vault。 雖然 “ContosoKeyVault” 作為本文中的 Key Vault 名稱，但您必須使用唯一的名稱。 請提供下列資訊：

* Vault 名稱 - **在這裡選取 Key Vault 名稱**。
* 資源群組名稱 - **在這裡選取資源群組名稱**。
* 位置 - **美國東部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="add-a-secret-to-key-vault"></a>將密碼新增至 Key Vault

我們將新增密碼，以協助說明其運作方式。 您可能正在儲存 SQL 連接字串，或任何您必須安全保存但可供您的應用程式使用的其他資訊。 在本教學課程中，密碼會稱為 **AppSecret**，並將在其中儲存 **MySecret** 的值。

輸入下列命令，以在 Key Vault 中建立稱為 **AppSecret** 並將儲存 **MySecret** 值的祕密：

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要以純文字檢視包含在祕密中的值：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令會顯示祕密資訊，包括 URI。 完成這些步驟之後，您的 Azure Key Vault 中應該會有密碼的 URI。 記下這項資訊。 您在稍後的步驟中需要此資訊。

## <a name="clone-the-repo"></a>複製存放庫

複製存放庫以便製作本機複本，讓您可以藉由執行下列命令以編輯來源：

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>安裝相依性

我們在此安裝相依性。 執行下列命令 cd key-vault-node-quickstart npm install

此專案使用了 2 個節點模組：

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>將 Web 應用程式發佈至 Azure

以下是我們必須執行的幾個步驟：

- 第 1 個步驟是建立 [Azure App Service](https://azure.microsoft.com/services/app-service/) 方案。 您可以在此方案中儲存多個 Web 應用程式。

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- 我們會接著建立 Web 應用程式。 在下列範例中，使用全域唯一的應用程式名稱 (有效的字元為 a-z、0-9 和 -) 取代 <app_name>。 執行階段設定為 NODE|6.9。 若要查看所有支援的執行階段，請執行 az webapp list-runtimes
    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    # PowerShell
    az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9"
    ```
    建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：
    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
    瀏覽至新建立的 Web 應用程式，您應會看到正常運作的 Web 應用程式。 以唯一的應用程式名稱取代 <app_name>。

    ```
    http://<app name>.azurewebsites.net
    ```
    上述命令也會建立具有 Git 功能的應用程式，讓您從本機 git 部署至 Azure。 
    本機 Git 已設定為使用 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git' 的 url

- 在前一個命令完成後，建立部署使用者，您可以將 Azure 遠端新增至您的本機 Git 存放庫。 使用從「為應用程式啟用 Git」取得的 Git 遠端 URL 取代 <url>。

    ```
    git remote add azure <url>
    ```

## <a name="enable-managed-service-identity"></a>啟用受控服務識別

Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 受控服務身分識別 (MSI) 可以輕易地解決此問題，因為 MSI 可在 Azure Active Directory (Azure AD) 中提供自動受控身分識別給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

執行 assign-identity 命令來建立此應用程式的識別：

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

此命令等同於前往入口網站，並在 Web 應用程式屬性中將 [受控服務識別] 切換為 [開啟]。

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>將權限指派給您的應用程式，以便從 Key Vault 讀取秘密

請記下或複製上述命令的輸出。 其格式應該如下：
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
然後，使用 Key Vault 名稱以及從上方複製的 PrincipalId 值，來執行這個命令：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>將 Node 應用程式部署至 Azure 並擷取祕密值

現在一切都已設定。 執行下列命令將應用程式部署至 Azure

```
git push azure master
```

在此之後，當您瀏覽 https://<app_name>.azurewebsites.net 時，您可以看見祕密值。
確定您已使用保存庫名稱取代名稱 <YourKeyVaultName>

## <a name="next-steps"></a>後續步驟

* [Azure Key Vault 首頁](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault 文件](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API 參考](https://docs.microsoft.com/rest/api/keyvault/)