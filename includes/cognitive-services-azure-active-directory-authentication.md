---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: f8d6e5de7f907ae78958b8c239649f55257bf7f2
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467531"
---
## <a name="authenticate-with-azure-active-directory"></a>向 Azure Active Directory 進行驗證

> [!IMPORTANT]
> 目前,**只有**電腦視覺 API、臉部 api、文字分析 API 和沉浸式讀取器支援使用 AZURE ACTIVE DIRECTORY (AAD) 進行驗證。

在前面幾節中, 我們示範了如何使用單一服務或多服務訂用帳戶金鑰, 針對 Azure 認知服務進行驗證。 雖然這些索引鍵提供快速且簡單的途徑來開始開發, 但它們在需要角色型存取控制的更複雜案例中很短暫。 讓我們看看使用 Azure Active Directory (AAD) 進行驗證所需的內容。

在下列各節中, 您將使用 Azure Cloud Shell 環境或 Azure CLI 來建立子域、指派角色, 以及取得持有人權杖來呼叫 Azure 認知服務。 如果您停滯, 會在每個區段中提供連結, 其中包含 Azure Cloud Shell/Azure CLI 中每個命令的所有可用選項。

### <a name="create-a-resource-with-a-custom-subdomain"></a>建立具有自訂子域的資源

第一個步驟是建立自訂子域。

1. 從開啟 Azure Cloud Shell 開始。 然後[選取訂用](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description)帳戶:

   ```azurecli-interactive
   Select-AzureSubscription -SubscriptionName <YOUR_SUBCRIPTION>
   ```

2. 接下來, 建立具有自訂子域的[認知服務資源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)。 子功能變數名稱稱必須是全域唯一的, 而且不能包含特殊字元, 例如: "."、"!"、","。

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 如果成功,**端點**應該會顯示資源的唯一子功能變數名稱稱。


### <a name="assign-a-role-to-a-service-principal"></a>將角色指派給服務主體

既然您已有與資源相關聯的自訂子域, 您就必須將角色指派給服務主體。

> [!NOTE]
> 請記住, AAD 角色指派最多可能需要五分鐘的時間來傳播。

1. 首先, 讓我們來註冊[AAD 應用程式](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)。

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   在下一個步驟中, 您將需要**ApplicationId** 。

2. 接下來, 您必須建立 AAD 應用程式的[服務主體](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)。

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > 如果您在 Azure 入口網站中註冊應用程式, 則會為您完成此步驟。

3. 最後一個步驟是[將「認知服務使用者」角色指派](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)給服務主體 (範圍限於資源)。 藉由指派角色, 您就會授與此資源的服務主體存取權。 您可以將相同的服務主體存取權授與您訂用帳戶中的多個資源。
   >[!NOTE]
   > 使用服務主體的 ObjectId, 而不是應用程式的 ObjectId。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>範例要求

在此範例中, 會使用密碼來驗證服務主體。 接著會使用提供的權杖來呼叫電腦視覺 API。

1. 取得您的**TenantId**:
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. 取得權杖:
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $password
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. 呼叫電腦視覺 API:
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

或者, 也可以使用憑證來驗證服務主體。 除了服務主體之外, 也支援透過另一個 AAD 應用程式委派許可權的使用者主體。 在此情況下, 當取得權杖時, 系統會提示使用者進行雙因素驗證, 而不是密碼或憑證。
