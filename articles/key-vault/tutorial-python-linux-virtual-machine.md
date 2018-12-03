---
title: 教學課程 - 如何在 Python 中搭配使用 Azure Key Vault 與 Azure Linux 虛擬機器 | Microsoft Docs
description: 教學課程：設定 ASP.NET Core 應用程式，以從 Key Vault 讀取祕密
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 5f56022be7968d3be65fd06fef791d859acf14c0
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585179"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-python"></a>教學課程：如何在 Python 中搭配使用 Azure Key Vault 與 Azure Linux 虛擬機器

Azure Key Vault 可協助您保護秘密，例如存取您的應用程式、服務和 IT 資源所需的 API 金鑰和資料庫連接字串。

在本教學課程中，您將執行讓 Azure Web 應用程式使用 Azure 資源的受控識別從 Azure Key Vault 讀取資訊所需的步驟。 本教學課程以 [Azure Web Apps](../app-service/app-service-web-overview.md) 為基礎。 在後續內容中，您將了解如何：

> [!div class="checklist"]
> * 建立金鑰保存庫。
> * 將秘密儲存在金鑰保存庫中。
> * 從金鑰保存庫擷取祕密。
> * 建立 Azure 虛擬機器。
> * 啟用虛擬機器的[受控識別](../active-directory/managed-identities-azure-resources/overview.md)。
> * 授與主控台應用程式從金鑰保存庫讀取資料所需的權限。
> * 從 Key Vault 擷取祕密

在我們繼續之前，請閱讀[基本概念](key-vault-whatis.md#basic-concepts)。

## <a name="prerequisites"></a>必要條件
* 所有平台：
  * Git ([下載](https://git-scm.com/downloads))。
  * Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 版或更新版本。 此工具適用於 Windows、Mac 和 Linux。

本教學課程會使用受控服務識別

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>什麼是受控服務識別？其運作方式為何？
我們要先了解 MSI，再繼續進行其他深入討論。 Azure Key Vault 可安全地儲存認證，因此認證不會在您的程式碼中，而是必須向 Azure Key Vault 進行驗證，才可加以擷取。 若要向 Key Vault 進行驗證，您必須要有認證！ 這是典型的啟動程序問題。 透過 Azure 和 Azure AD 的強大功能，MSI 可提供「啟動程序身分識別」，讓您更輕鬆地開始執行工作。

其運作方式如下！ 當您為 Azure 服務 (例如虛擬機器、App Service 或 Functions) 啟用 MSI 時，Azure 會在 Azure Active Directory 中建立服務執行個體的[服務主體](key-vault-whatis.md#basic-concepts)，並將服務主體的認證插入服務執行個體中。 

![MSI](media/MSI.png)

接下來，您的程式碼會呼叫 Azure 資源上可用的本機中繼資料服務，以取得存取權杖。
您的程式碼會使用從本機 MSI_ENDPOINT 取得的存取權杖，向 Azure Key Vault 服務進行驗證。 

## <a name="log-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

選取資源群組名稱，並填入預留位置。
下列範例會在美國西部位置建立一個資源群組：

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

本文將一律使用您剛才建立的資源群組。

## <a name="create-a-key-vault"></a>建立金鑰保存庫

接下來，您會在上一個步驟中建立的資源群組中建立金鑰保存庫。 請提供下列資訊：

* 金鑰保存庫名稱：此名稱必須是 3-24 個字元的字串，且只能包含 0-9、a-z、A-Z 和 -。
* 資源群組名稱。
* 位置：**美國西部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="add-a-secret-to-the-key-vault"></a>將秘密新增至金鑰保存庫

我們將新增密碼，以協助說明其運作方式。 您可以儲存 SQL 連接字串，或任何您必須安全保存但可供應用程式使用的其他資訊。

輸入下列命令，以在名為 **AppSecret** 的金鑰保存庫中建立秘密。 此秘密會儲存值 **MySecret**。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令來建立 VM。

下列範例會建立名為 myVM 的 VM，並新增名為 azureuser 的使用者帳戶。 `--generate-ssh-keys` 參數用來自動產生 SSH 金鑰，並將它放在預設金鑰位置 (~/.ssh)。 若要改為使用一組特定金鑰，請使用 `--ssh-key-value` 選項。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

建立虛擬機器和支援資源需要幾分鐘的時間。 下列範例輸出顯示 VM 建立作業成功。

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

請注意 VM 輸出中您自己的 `publicIpAddress`。 後續步驟會使用此位址來存取 VM。

## <a name="assign-identity-to-virtual-machine"></a>將身分識別指派給虛擬機器
在此步驟中，我們會藉由在 Azure CLI 中執行下列命令，來對虛擬機器建立系統指派的身分識別

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

請注意如下所示的 systemAssignedIdentity。 上述命令的輸出會是 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-virtual-machine-identity-permission-to-key-vault"></a>對 Key Vault 授與虛擬機器身分識別權限
現在，我們可以執行下列命令，來對 Key Vault 授與上面所建立的身分識別權限

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>登入虛擬機器

您可以遵循此[教學課程](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

## <a name="create-and-run-sample-python-app"></a>建立和執行 Python 應用程式範例

以下是是名為 "Sample.py" 的檔案範例。 此檔案會使用[要求](http://docs.python-requests.org/master/)程式庫來發出 HTTP GET 呼叫。

## <a name="edit-samplepy"></a>編輯 Sample.py
建立 Sample.py 後，請開啟檔案並複製下列程式碼

以下是 2 個步驟的程序。 
1. 從 VM 上的本機 MSI 端點擷取權杖，進而從 Azure Active Directory 擷取權杖
2. 將權杖傳遞給 Key Vault 並擷取祕密 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets a access_token from Azure Active Directory using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to Key Vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

在執行期間，您應該會看到祕密值 

```
python Sample.py
```

上述程式碼會示範如何在 Azure Windows 虛擬機器中使用 Azure Key Vault 執行作業。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
