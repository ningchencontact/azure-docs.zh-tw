---
title: 教學課程：如何在 .NET 中透過 Azure Web 應用程式使用 Azure Key Vault | Microsoft Docs
description: 教學課程：設定 ASP.NET Core 應用程式，以從 Key Vault 讀取祕密
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 9cc22e158a9473b7b60f7e8bcb57174abc1fb8cc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218547"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>教學課程：如何在 .NET 中透過 Azure Web 應用程式使用 Azure Key Vault

Azure Key Vault 可協助您保護秘密，例如存取您的應用程式、服務和 IT 資源所需的 API 金鑰和資料庫連接字串。

在本教學課程中，您將執行讓 Azure Web 應用程式使用 Azure 資源的受控識別從 Azure Key Vault 讀取資訊所需的步驟。 本教學課程以 [Azure Web Apps](../app-service/app-service-web-overview.md) 為基礎。 在後續內容中，您將了解如何：

> [!div class="checklist"]
> * 建立金鑰保存庫。
> * 將秘密儲存在金鑰保存庫中。
> * 從金鑰保存庫擷取祕密。
> * 建立 Azure Web 應用程式。
> * 啟用 Web 應用程式的[受控識別](../active-directory/managed-identities-azure-resources/overview.md)。
> * 授與 Web 應用程式從金鑰保存庫讀取資料所需的權限。
> * 在 Azure 上執行 Web 應用程式

在我們繼續之前，請閱讀[基本概念](key-vault-whatis.md#basic-concepts)。

## <a name="prerequisites"></a>必要條件

* 在 Windows 上：
  * [.NET Core 2.1 SDK 或更新版本](https://www.microsoft.com/net/download/windows)

* 在 Mac 上：
  * 請參閱 [Visual Studio for Mac 的新功能](https://visualstudio.microsoft.com/vs/mac/)。

* 所有平台：
  * Git ([下載](https://git-scm.com/downloads))。
  * Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 版或更新版本。 此工具適用於 Windows、Mac 和 Linux。
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>什麼是受控服務識別？其運作方式為何？
 我們要先了解 MSI，再繼續進行其他深入討論。 Azure Key Vault 可安全地儲存認證，因此認證不會在您的程式碼中，而是必須向 Azure Key Vault 進行驗證，才可加以擷取。 若要向 Key Vault 進行驗證，您必須要有認證！ 這是典型的啟動程序問題。 透過 Azure 和 Azure AD 的強大功能，MSI 可提供「啟動程序身分識別」，讓您更輕鬆地開始執行工作。

其運作方式如下！ 當您為 Azure 服務 (例如虛擬機器、App Service 或 Functions) 啟用 MSI 時，Azure 會在 Azure Active Directory 中建立服務執行個體的[服務主體](key-vault-whatis.md#basic-concepts)，並將服務主體的認證插入服務執行個體中。 

![MSI](media/MSI.png)

接下來，您的程式碼會呼叫 Azure 資源上可用的本機中繼資料服務，以取得存取權杖。
您的程式碼會使用從本機 MSI_ENDPOINT 取得的存取權杖，向 Azure Key Vault 服務進行驗證。 

接著我們就開始進行教學課程。

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

若要以純文字檢視包含在祕密中的值：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令會顯示祕密資訊，包括 URI。 完成這些步驟之後，您應該會有祕密在金鑰保存庫中的 URI。 請記下此資訊。 您在後續的步驟中將需要此資訊。

## <a name="create-a-net-core-web-app"></a>建立 .NET Core Web 應用程式

依照本[教學課程](../app-service/app-service-web-get-started-dotnet.md)的指示建立 .NET Core Web 應用程式並**發佈**至 Azure，**或**觀賞下列影片
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>開啟及編輯解決方案

1. 瀏覽至 [頁面] > About.cshtml.cs 檔案。
2. 安裝以下兩個 NuGet 套件
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. 在 About.cshtml.cs 檔案中匯入下列內容

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. 您在 AboutModel 類別中的程式碼會顯示如下
    ```
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;             

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }            
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```


## <a name="run-the-app"></a>執行應用程式

在 Visual Studio 2017 的主功能表中，選取 [偵錯] > [啟動] (偵錯/不偵錯)。 在瀏覽器出現時，移至 [關於] 頁面。 **AppSecret** 的值隨即顯示。

## <a name="enable-a-managed-identity-for-the-web-app"></a>啟用 Web 應用程式的受控識別

Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 [Azure 資源受控識別概觀](../active-directory/managed-identities-azure-resources/overview.md)可在 Azure Active Directory (Azure AD) 中將受控識別自動提供給 Azure 服務，而降低解決此問題的難度。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

1. 返回 Azure CLI。
2. 執行 assign-identity 命令來建立此應用程式的識別： 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   請注意，您必須將 <YourAppName> 取代為 Azure 上已發佈的應用程式名稱，也就是說，如果已發佈的應用程式名稱為 MyAwesomeapp.azurewebsites.net，請將 <YourAppName> 取代為 MyAwesomeapp
 
 上述命令的輸出會顯示如下。當您將應用程式發佈至 Azure 時，請記下 PrincipalId。 其格式應為：
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>此程序中的命令等同於前往[入口網站](https://portal.azure.com)，並在 Web 應用程式屬性中將 [身分識別/系統指派] 設定切換為 [開啟]。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>將權限指派給您的應用程式，以便從 Key Vault 讀取秘密
        
然後，使用金鑰保存庫的名稱和 **PrincipalId** 的值來執行此命令：

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>將 Web 應用程式發佈至 Azure

再次將此應用程式發佈至 Azure，以 Web 應用程式的形式即時查看，並確認您可以擷取祕密值。

1. 在 Visual Studio 中，選取 **key-vault-dotnet-core-quickstart** 專案。
2. 選取 [發佈] > [開始]。
3. 選取 [建立] 。

在上述命令中，您會被賦予 App Service 的身分識別 (MSI) 權限，而可在您的 Key Vault 上執行**取得**與**列出**作業。 <br />
現在當您執行應用程式時，您應該會看到擷取的秘密值。 

這樣就大功告成了。 您已在 .NET 中成功建立可對 Key Vault 儲存和擷取祕密的 Web 應用程式。
