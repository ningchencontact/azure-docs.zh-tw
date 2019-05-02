---
title: 快速入門 - 使用 Node Web 應用程式從 Azure Key Vault 設定及擷取祕密 | Microsoft Docs
description: 在本快速入門中，您會使用 Node Web 應用程式從 Azure Key Vault 設定及擷取祕密
services: key-vault
author: mbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: a9b92b691974eb9a14feaf97d760aa056ad21783
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696046"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>快速入門：使用 Node Web 應用程式從 Azure Key Vault 設定及擷取祕密 

本快速入門說明如何將祕密儲存在 Azure Key Vault 中，以及如何使用 Web 應用程式加以擷取。 使用 Key Vault 可協助保護資訊的安全。 若要查看祕密值，您必須在 Azure 上執行此快速入門。 此快速入門使用 Node.js 與適用於 Azure 資源的受控識別。 您會了解如何：

* 建立金鑰保存庫。
* 將秘密儲存在金鑰保存庫中。
* 從金鑰保存庫擷取祕密。
* 建立 Azure Web 應用程式。
* 啟用 Web 應用程式的[受控識別](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)。
* 授與 Web 應用程式從金鑰保存庫讀取資料所需的權限。

繼續之前，請先確定您已熟悉 [Key Vault 的基本概念](key-vault-whatis.md#basic-concepts)。

> [!NOTE]
> Key Vault 是一個中央存放庫，可透過程式設計方式儲存秘密。 但若要這樣做，應用程式和使用者必須要先向 Key Vault 進行驗證，也就是出具祕密。 為符合安全性最佳做法，第一個秘密必須要定期輪替。 
>
> 使用 [Azure 資源的受控服務識別](../active-directory/managed-identities-azure-resources/overview.md)時，在 Azure 中執行的應用程式將會獲得一個由 Azure 自動管理的身分識別。 這有助於解決*祕密導入問題*，如此，使用者和應用程式即可遵循最佳做法，且不需要擔心輪替第一個祕密的問題。

## <a name="prerequisites"></a>必要條件

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 或更新版本。 此快速入門需要您在本機執行 Azure CLI。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest)。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

選取資源群組名稱，並填入預留位置。
下列範例會在美國東部位置建立一個資源群組。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

本文將一律使用您剛才建立的資源群組。

## <a name="create-a-key-vault"></a>建立金鑰保存庫

接下來，您會使用在上一個步驟中建立的資源群組建立金鑰保存庫。 雖然本文使用 “ContosoKeyVault” 作為名稱，但您必須使用唯一的名稱。 請提供下列資訊：

* 金鑰保存庫名稱。
* 資源群組名稱。 名稱必須是 3-24 個字元的字串，且只能包含 0-9、a-z、A-Z 和連字號 (-)。
* 位置：**美國東部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="add-a-secret-to-the-key-vault"></a>將秘密新增至金鑰保存庫

我們將新增密碼，以協助說明其運作方式。 您可以儲存 SQL 連接字串，或任何您必須安全保存但可供應用程式使用的其他資訊。 在本教學課程中，密碼會稱為 **AppSecret**，並將在其中儲存 **MySecret** 的值。

輸入下列命令，以在名為 **AppSecret** 的金鑰保存庫中建立秘密。 此秘密會儲存值 **MySecret**。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要以純文字檢視包含在祕密中的值：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令會顯示祕密資訊，包括 URI。 完成這些步驟之後，您應該會有祕密在金鑰保存庫中的 URI。 請記下此資訊。 您在後續的步驟中將需要此資訊。

## <a name="clone-the-repo"></a>複製存放庫

複製存放庫，以建立可用來編輯來源的本機複本。 執行以下命令：

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>安裝相依性

執行下列命令以安裝相依項目：

```
cd key-vault-node-quickstart
npm install
```

此專案使用兩個節點模組：[ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 和 [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)。

## <a name="publish-the-web-app-to-azure"></a>將 Web 應用程式發佈至 Azure

建立 [Azure App Service](https://azure.microsoft.com/services/app-service/) 方案。 您可以在此方案中儲存多個 Web 應用程式。

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
接下來，建立 Web 應用程式。 在下列範例中，使用全域唯一的應用程式名稱 (有效的字元為 a-z、0-9 和 -) 取代 `<app_name>`。 執行階段設定為 NODE|6.9。 若要查看所有支援的執行階段，請執行 `az webapp list-runtimes`。

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
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
瀏覽至新建立的 Web 應用程式，您應會看到該應用程式運作正常。 以唯一的應用程式名稱取代 `<app_name>`。

    ```
    http://<app name>.azurewebsites.net
    ```
前述命令也會建立具有 Git 功能的應用程式，讓您從本機 Git 存放庫部署至 Azure。 本機 Git 存放庫設定為此 URL：`https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`。

在完成前述命令之後，您可以將 Azure 遠端新增到您的本機 Git 存放庫。 使用 Git 存放庫的 URL 取代 `<url>`。

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>啟用 Web 應用程式的受控識別

Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 [Azure 資源受控識別概觀](../active-directory/managed-identities-azure-resources/overview.md)可在 Azure Active Directory (Azure AD) 中將受控識別自動提供給 Azure 服務，而降低解決此問題的難度。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

執行 assign-identity 命令來建立此應用程式的識別：

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

此命令等同於前往入口網站，並在 Web 應用程式屬性中將 [身分識別/系統指派] 設定切換為 [開啟]。

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>將權限指派給您的應用程式，以便從 Key Vault 讀取秘密

請記下前一個命令的輸出。 其格式應該如下：
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
然後，使用金鑰保存庫的名稱和 **PrincipalId** 的值來執行下列命令：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>將 Node 應用程式部署至 Azure 並擷取祕密值

執行下列命令將應用程式部署至 Azure：

```
git push azure master
```

在此之後，當您瀏覽至 `https://<app_name>.azurewebsites.net` 時，您可以看見祕密值。 確定您已使用保存庫名稱取代名稱 `<YourKeyVaultName>`。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure SDK for Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
