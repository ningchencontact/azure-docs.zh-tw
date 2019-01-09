---
title: 教學課程：在 .NET 中透過 Azure Web 應用程式使用 Azure Key Vault - Azure Key Vault | Microsoft Docs
description: 教學課程 - 設定 ASP.NET Core 應用程式，以從 Key Vault 讀取祕密
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: b6dbae0f721983920c2073927fff74100528678e
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998790"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>教學課程：在 .NET 中透過 Azure Web 應用程式使用 Azure Key Vault

Azure Key Vault 可協助您保護秘密，例如 API 金鑰和資料庫連接字串。 它可供您存取您的應用程式、服務和 IT 資源。

在本教學課程中，您會了解如何建立可從 Azure 金鑰保存庫讀取資訊的 Azure Web 應用程式。 此程序會使用 Azure 資源的受控識別。 如需有關 Azure Web 應用程式的詳細資訊，請參閱 [Azure App Service](../app-service/overview.md)。

本文將說明如何：

> [!div class="checklist"]
> * 建立金鑰保存庫。
> * 將秘密儲存在金鑰保存庫中。
> * 從金鑰保存庫擷取祕密。
> * 建立 Azure Web 應用程式。
> * 啟用 Web 應用程式的[受控識別](../active-directory/managed-identities-azure-resources/overview.md)。
> * 授與 Web 應用程式從金鑰保存庫讀取資料所需的權限。
> * 在 Azure 上執行 Web 應用程式。

在繼續之前，請閱讀 [Key Vault 基本概念](key-vault-whatis.md#basic-concepts)。

## <a name="prerequisites"></a>必要條件

* 在 Windows 上：
  * [.NET Core 2.1 SDK 或更新版本](https://www.microsoft.com/net/download/windows)

* 在 Mac 上：
  * [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)

* 所有平台：
  * [Git](https://git-scm.com/downloads)
  * Azure 訂用帳戶 <br />(如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。)
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 版或更新版本，適用於 Windows、Mac 和 Linux
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>受控服務識別及其運作方式

Azure Key Vault 可安全地儲存認證，因此認證不在您的程式碼中。 不過，您需要向 Azure Key Vault 驗證才能擷取您的金鑰。 若要向 Key Vault 進行驗證，您必須要有認證。 這是典型的啟動程序難題。 受控服務識別 (MSI) 可藉由提供可簡化此程序的「啟動程序身分識別」來解決此問題。

當您對 Azure 服務 (例如：虛擬機器、App Service 或 Functions) 啟用 MSI 時，Azure 會建立[服務主體](key-vault-whatis.md#basic-concepts)。 MSI 會在 Azure Active Directory (Azure AD) 中為服務執行個體執行此作業，並將服務主體的認證插入該執行個體中。

![MSI 圖表](media/MSI.png)

接下來，您的程式碼會呼叫 Azure 資源上可用的本機中繼資料服務，以取得存取權杖。 您的程式碼會使用從本機 MSI_ENDPOINT 取得的存取權杖，向 Azure Key Vault 服務進行驗證。

## <a name="sign-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

1. 使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。
1. 選取資源群組名稱，並填入預留位置。 下列範例會在美國西部位置建立一個資源群組：

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

您會在本教學課程中使用此資源群組。

## <a name="create-a-key-vault"></a>建立金鑰保存庫

若要在資源群組中建立金鑰保存庫，請提供下列資訊：

* 金鑰保存庫名稱：可以只包含數字、字母和連字號的 3 到 24 個字元的字串 (例如：0-9、a-z、A-Z 和 - )
* 資源群組名稱
* 位置：美國西部

在 Azure CLI 中輸入下列命令：

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。

## <a name="add-a-secret-to-the-key-vault"></a>將秘密新增至金鑰保存庫

您現在可以新增祕密。 這可以是 SQL 連接字串，或任何您必須安全保存並可供應用程式使用的其他資訊。

輸入下列命令，以在名為 **AppSecret** 的金鑰保存庫中建立秘密。 此秘密會儲存值 **MySecret**。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要以純文字檢視祕密內含的值，請輸入下列命令：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令會顯示祕密資訊，包括 URI。 完成這些步驟之後，您應該會有祕密在金鑰保存庫中的 URI。 請記下此資訊。 您在後續的步驟中將需要此資訊。

## <a name="create-a-net-core-web-app"></a>建立 .NET Core Web 應用程式

依照本[教學課程](../app-service/app-service-web-get-started-dotnet.md)的指示建立 .NET Core Web 應用程式並將它**發佈**至 Azure。 您也可以觀看下列影片：

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>開啟及編輯解決方案

1. 瀏覽至 [頁面] > [About.cshtml.cs] 檔案。
2. 安裝這些 NuGet 套件：
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. 在 About.cshtml.cs 檔案中匯入下列程式碼：

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. 您在 AboutModel 類別中的程式碼如下所示：

   ```csharp
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

1. 在 Visual Studio 2017 的主功能表中，選取 [偵錯] > [啟動] (偵錯/不偵錯)。 
1. 在瀏覽器出現時，移至 [關於] 頁面。
1. **AppSecret** 的值隨即顯示。

## <a name="enable-a-managed-identity-for-the-web-app"></a>啟用 Web 應用程式的受控識別

Azure Key Vault 可安全地儲存認證和其他祕密，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 [Azure 資源受控識別概觀](../active-directory/managed-identities-azure-resources/overview.md)可在 Azure AD 中將受控識別自動提供給 Azure 服務，協助解決此問題。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

1. 在 Azure CLI 中，執行 assign-identity 命令來建立此應用程式的身分識別：

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >您必須以 Azure 上已發佈的應用程式名稱取代 \<YourAppName\>。 例如，如果已發佈的應用程式名稱為 **MyAwesomeapp.azurewebsites.net**，請以 **MyAwesomeapp** 取代 \<YourAppName\>。

1. 當您將應用程式發佈至 Azure 時，請記下 `PrincipalId`。 步驟 1 中命令的輸出格式應該如下：

   ```json
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>此程序中的命令等同於前往[入口網站](https://portal.azure.com)，並在 Web 應用程式屬性中將 [身分識別/系統指派] 設定切換為 [開啟]。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>將權限指派給您的應用程式，以便從 Key Vault 讀取秘密

在下列命令中，以您的金鑰保存庫名稱取代 \<YourKeyVaultName\>，並以 **PrincipalId** 的值取代 \<PrincipalId\>：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

此命令會賦予 App Service 的身分識別 (MSI) 權限，以在金鑰保存庫上執行**取得**與**列出**作業。

## <a name="publish-the-web-application-to-azure"></a>將 Web 應用程式發佈至 Azure

再次將 Web 應用程式發佈至 Azure，以查看即時 Web 應用程式可以擷取祕密值。

1. 在 Visual Studio 中，選取 **key-vault-dotnet-core-quickstart** 專案。
2. 選取 [發佈] > [開始]。
3. 選取 [建立] 。

當您執行應用程式時，您應會發現它可擷取您的秘密值。

現在，您已在 .NET 中成功建立 Web 應用程式，而該應用程式可對 Key Vault 儲存和擷取祕密。

## <a name="next-steps"></a>後續步驟

>[!div class="nextstepaction"]
>[Azure Key Vault 開發人員指南](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
