---
title: 教學課程 - 如何在 .NET 中搭配使用 Azure Key Vault 與 Azure Linux 虛擬機器 - Azure Key Vault | Microsoft Docs
description: 教學課程：設定 ASP.NET Core 應用程式，以從 Key Vault 讀取祕密
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 969213f4006aa842711459fed7a76d08a9d3ce37
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707061"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-net"></a>教學課程：如何在 .NET 中搭配使用 Azure Key Vault 與 Azure Linux 虛擬機器

Azure Key Vault 可協助您保護秘密，例如存取您的應用程式、服務和 IT 資源所需的 API 金鑰和資料庫連接字串。

在本教學課程中，您會執行一些必要步驟，讓主控台應用程式能使用 Azure 資源的受控識別從 Azure Key Vault 讀取資訊。 在後續內容中，您將了解如何：

> [!div class="checklist"]
> * 建立金鑰保存庫。
> * 將秘密儲存在金鑰保存庫中。
> * 從金鑰保存庫擷取祕密。
> * 建立 Azure 虛擬機器。
> * 啟用虛擬機器的[受控識別](../active-directory/managed-identities-azure-resources/overview.md)。
> * 授與主控台應用程式從金鑰保存庫讀取資料所需的權限。
> * 從 Key Vault 擷取祕密

在進一步討論之前，請先閱讀[基本概念](key-vault-whatis.md#basic-concepts)。

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

## <a name="sign-in-to-azure"></a>登入 Azure

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

* 金鑰保存庫名稱：名稱必須是 3-24 個字元的字串，且只能包含 0-9、a-z、A-Z 和 -。
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

使用 [az vm create](/cli/azure/vm) 命令來建立 VM。

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

在此步驟中，我們會藉由執行下列命令，來對虛擬機器建立系統指派的身分識別

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

請注意以下顯示的 systemAssignedIdentity。 上述命令的輸出會是 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-vm-identity-permission-to-key-vault"></a>對 Key Vault 授與 VM 身分識別權限

現在，我們可以執行下列命令，來對 Key Vault 授與上面所建立的身分識別權限

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>登入虛擬機器

現在，使用終端機來登入虛擬機器

```
ssh azureuser@<PublicIpAddress>
```

## <a name="install-dot-net-core-on-linux"></a>在 Linux 上安裝 .NET Core

### <a name="register-the-microsoft-product-key-as-trusted-run-the-following-two-commands"></a>將 Microsoft 產品金鑰註冊為可信任。 執行下列兩個命令

```
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
```

### <a name="set-up-desired-version-host-package-feed-based-on-operating-system"></a>根據作業系統設定所需的版本裝載套件饋送

```
 
# Ubuntu 16.04 / Linux Mint 18
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
 
# Ubuntu 14.04 / Linux Mint 17
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
```

### <a name="install-net-core"></a>安裝 .NET Core

並檢查 .NET 版本

```
sudo apt-get install dotnet-sdk-2.1.4
dotnet --version
```

## <a name="create-and-run-sample-dot-net-app"></a>建立和執行 .NET 應用程式範例

藉由執行下列命令，您應該會看到 "Hello World" 列印至主控台

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-console-app"></a>編輯主控台應用程式

開啟 Program.cs 檔案並新增這些套件
```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

然後變更類別檔案，以包含下列程式碼。 此程序包含兩個步驟。

1. 從 VM 上的本機 MSI 端點擷取權杖，進而從 Azure Active Directory 擷取權杖
2. 將權杖傳遞給 Key Vault 並擷取祕密 

```
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
            var token = GetToken();

            // Step 2: Fetch the secret value from Key Vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];   
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

上述程式碼會示範如何在 Azure Linux 虛擬機器中使用 Azure Key Vault 執行作業。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
