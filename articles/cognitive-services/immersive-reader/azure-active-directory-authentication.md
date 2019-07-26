---
title: Azure Active Directory (Azure AD) 驗證
titleSuffix: Azure Cognitive Services
description: 沉浸式讀取器 SDK 的參考
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 8af56d0d7c0f320e607421f12effdb9b37d5f78d
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444632"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>搭配使用 Azure Active Directory (Azure AD) 驗證與沉浸式讀取器服務

在下列各節中, 您將使用 Azure Cloud Shell 環境或 Azure CLI, 以自訂子域建立新的沉浸式讀取器資源, 然後在您的 Azure 租使用者中設定 Azure AD。 完成初始設定之後, 您將會呼叫 Azure AD 來取得存取權杖, 類似于使用沉浸式讀取器 SDK 時的執行方式。 如果您停滯, 會在每個區段中提供連結, 其中每個 Azure CLI 命令都有可用的選項。

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>建立具有自訂子域的沉浸式讀取器資源

1. 從開啟[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)開始。 然後[選取訂用](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description)帳戶:

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. 接下來, 建立具有自訂子域的[沉浸式讀取器資源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0)。 

   >[!NOTE]
   > 使用 launchAsync 函數啟動讀取器時, 會在沉浸式讀取器 SDK 中使用子功能變數名稱稱。

   -SkuName 可以是 F0 (免費層) 或 S0 (標準層, 在公開預覽期間也免費)。 S0 層具有較高的呼叫率限制, 而且沒有每月的呼叫數目配額。

   -Location 可以是下列任何一項: `eastus`、 `westus`、 `australiaeast`、 `centralindia`、 `japaneast`、 `northeurope`、`westeurope`
   
   -CustomSubdomainName 必須是全域唯一的, 而且不能包含特殊字元, 例如: "."、"!"、","。


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   如果成功, 資源**端點**應該會顯示資源的唯一子功能變數名稱稱。

   在這裡, 我們會將新建立的資源物件捕獲到 **$resource**變數中, 稍後當授與此資源的存取權時, 將會用到它。


   >[!NOTE]
   > 如果您在 Azure 入口網站中建立資源, 則會使用資源「名稱」做為自訂子域。 您可以在入口網站中檢查子功能變數名稱稱, 方法是前往 [資源] [總覽] 頁面, 然後在其中列出的端點中尋找`https://[SUBDOMAIN].cognitiveservices.azure.com/`子域, 例如。 當您稍後需要取得可與 SDK 整合的子域時, 您也可以在這裡查看。

   如果資源是在入口網站中建立, 您也可以立即[取得現有的資源](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0)。

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```   

## <a name="assign-a-role-to-a-service-principal"></a>將角色指派給服務主體

現在您已有與資源相關聯的自訂子域, 您必須將角色指派給服務主體。

1. 首先, 讓我們[建立一個 Azure AD 應用程式](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)。

   >[!NOTE]
   > 取得驗證權杖時, 將會使用密碼 (也稱為「用戶端密碼」)。

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   在這裡, 我們會將新建立的 Azure AD 應用程式物件捕捉到 **$aadApp**變數中, 以供下一個步驟使用。   

2. 接下來, 您必須建立 Azure AD 應用程式的[服務主體](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0)。

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   在這裡, 我們會將新建立的服務主體物件捕捉到 **$principal**變數中, 以供下一個步驟使用。


3. 最後一個步驟是[將「認知服務使用者」角色指派](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0)給服務主體 (範圍限於資源)。 藉由指派角色, 您會將此資源的存取權授與服務主體。 您可以將相同的服務主體存取權授與您訂用帳戶中的多個資源。

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > 您必須針對您建立的每個沉浸式讀取器資源執行此步驟。 例如, 如果您為不同的全球區域建立多個資源, 則必須針對每個區域資源執行此步驟, 如此一來, Azure AD 的驗證就能用於所有的工作。 或者, 如果您稍後再建立新的資源, 您也必須為該新資源執行此角色指派步驟。


## <a name="obtain-an-azure-ad-token"></a>取得 Azure AD token

在此範例中, 您的密碼會用來驗證服務主體, 以取得 Azure AD token。

1. 取得您的**TenantId**:
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. 取得權杖:
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > 沉浸式讀取器 SDK 會使用權杖的 AccessToken 屬性, 例如 $token。AccessToken. 如需詳細資訊, 請參閱 SDK[參考](reference.md)和程式碼[範例](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples)。

或者, 也可以使用憑證來驗證服務主體。 除了服務主體之外, 也支援透過另一個 Azure AD 應用程式委派的許可權來提供使用者主體。 在此情況下, 當取得權杖時, 系統會提示使用者進行雙因素驗證, 而不是密碼或憑證。

## <a name="next-steps"></a>後續步驟

* 檢視[教學課程](./tutorial.md)以查看您還可以使用沈浸式閱讀程式 SDK 來執行哪些作業
* 探索[沈浸式閱讀程式 SDK](https://github.com/Microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)