---
title: 使用 .NET 進行 Azure Key Vault 的服務對服務驗證
description: 使用 Microsoft.Azure.Services.AppAuthentication 程式庫以利用 .NET 向 Azure Key Vault 進行驗證。
keywords: Azure Key Vault 驗證的本機認證
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 79d4254de40ef787b30eb4f483c86383a928ee1f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566232"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>使用 .NET 進行 Azure Key Vault 的服務對服務驗證

若要向 Azure Key Vault 中，您需要 Azure Active Directory (AD) 認證，共用的密碼或憑證。 

管理這類認證並不簡單，而且我們很容易會將認證包含在來源或設定檔中，藉此與應用程式結合在一起。  .NET 程式庫的 `Microsoft.Azure.Services.AppAuthentication` 可簡化此問題。 它會使用開發人員的認證，在本機開發期間進行驗證。 當解決方案在稍後部署至 Azure 時，程式庫會自動切換至應用程式認證。    在本機開發期間使用開發人員認證會比較安全，因為您不需要建立 Azure AD 認證，或在開發人員之間共用認證。

`Microsoft.Azure.Services.AppAuthentication` 程式庫會自動管理驗證，這可讓您將焦點放在您的方案，而不是認證上。  它支援使用 Microsoft Visual Studio、 Azure CLI 或 Azure AD 的整合式驗證的本機開發。 如果部署到支援受控執行個體的 Azure 資源，程式庫會自動使用 [Azure 資源的受控識別](../active-directory/msi-overview.md)。 不需要任何程式碼或設定變更。 當受控識別無法使用，或在本機開發期間無法判斷開發人員的安全性內容時，程式庫也支援直接使用 Azure AD [用戶端認證](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

## <a name="using-the-library"></a>使用程式庫

對於 .NET 應用程式，使用受控識別最簡單的方式是透過 `Microsoft.Azure.Services.AppAuthentication` 套件。 如何開始使用：

1. 將 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 套件的參考新增至應用程式。 

2. 新增下列程式碼：

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` 類別會快取記憶體中的權杖，並在到期之前從 Azure AD 擷取權杖。 因此，您不再需要先檢查到期日，再呼叫 `GetAccessTokenAsync` 方法。 當您想要使用權杖時，可直接呼叫方法。 

`GetAccessTokenAsync` 方法需要資源識別碼。 若要深入了解，請參閱[哪些 Azure 服務支援 Azure 資源的受控識別](../active-directory/msi-overview.md)。

## <a name="local-development-authentication"></a>本機開發驗證

針對本機開發，有兩個主要的驗證案例：[向 Azure 服務](#authenticating-to-azure-services)，並[向自訂服務](#authenticating-to-custom-services)。

### <a name="authenticating-to-azure-services"></a>向 Azure 服務驗證

本機電腦不支援 Azure 資源的受控識別。  因此，`Microsoft.Azure.Services.AppAuthentication` 程式庫會使用您的開發人員認證，以在本機開發環境中執行。 當解決方案部署至 Azure 時，程式庫會使用受控識別切換至 OAuth 2.0 用戶端的認證授與流程。  這表示您可以放心地在本機或遠端測試相同程式碼。

針對本機開發，`AzureServiceTokenProvider` 會使用 **Visual Studio**、**Azure 命令列介面** (CLI)，或 **Azure AD 整合式驗證**來擷取權杖。 會依序嘗試每個選項，而程式庫會使用第一個成功的選項。 如果沒有選項可用，`AzureServiceTokenProviderException` 會擲回包含詳細資訊的例外狀況。

### <a name="authenticating-with-visual-studio"></a>使用 Visual Studio 進行驗證

向 Visual Studio 有下列先決條件：

1. [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/)或更新版本。

2. [適用於 Visual Studio 的應用程式驗證延伸模組](https://go.microsoft.com/fwlink/?linkid=862354)、 可另外的延伸模組，Visual Studio 2017 Update 5 和配套產品在更新 6 和更新版本。 更新 6 或更新版本中，您可以藉由選取 在 Visual Studio 安裝程式中的 Azure 開發工具來確認安裝應用程式驗證擴充功能。
 
登入 Visual Studio，並使用**工具**&nbsp;>&nbsp;**選項**&nbsp;>&nbsp;**Azure 服務驗證**選取適用於本機開發帳戶。 

如果您使用 Visual Studio 時遇到問題，例如發生有關權杖提供者檔案的錯誤，請仔細檢閱這些步驟。 

此時也可能需要重新驗證您的開發人員權杖。 若要這樣做，請移至 [工具]  &nbsp;>&nbsp;[選項]  >[Azure&nbsp;服務驗證]  ，然後在選取的帳戶下尋找 [重新驗證]  連結。&nbsp;  選取該項目並進行驗證。 

### <a name="authenticating-with-azure-cli"></a>使用 Azure CLI 進行驗證

若要將 Azure CLI 用於本機開發：

1. 安裝 [Azure CLI v2.0.12](/cli/azure/install-azure-cli) 或更新版本。 升級較早的版本。 

2. 使用 **az login** 登入 Azure。

使用 `az account get-access-token` 驗證存取權。  如果您收到錯誤訊息，請確認您已成功完成步驟 1。 

如果 Azure CLI 未安裝至預設目錄中，您可能會收到一則錯誤訊息，指出 `AzureServiceTokenProvider` 找不到 Azure CLI 的路徑。  使用**AzureCLIPath**環境變數來定義 Azure CLI 安裝資料夾。 必要時，`AzureServiceTokenProvider` 會將 **AzureCLIPath** 環境變數中指定的目錄新增至**路徑**環境變數。

如果您使用多個帳戶登入 Azure CLI，或您的帳戶具有多個訂用帳戶的存取權，則需要指定要使用的特定訂用帳戶。  若要這樣做，請使用：

```
az account set --subscription <subscription-id>
```

此命令只會在失敗時產生輸出。  若要確認目前的帳戶設定，請使用：

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>向 Azure AD 驗證

若要使用 Azure AD 驗證，請確認下列事項：

- 您的內部部署 Active Directory 會[同步至 Azure AD](../active-directory/connect/active-directory-aadconnect.md)。

- 您的程式碼正在已加入網域的電腦上執行。


### <a name="authenticating-to-custom-services"></a>向自訂服務驗證

當服務呼叫 Azure 服務時，上述步驟即會開始作用，因為 Azure 服務允許存取使用者和應用程式。  

建立會呼叫自訂服務的服務時，請在本機開發驗證上使用 Azure AD 用戶端認證。  有兩個選項： 

1.  使用服務主體登入 Azure：

    1.  [建立服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

    2.  使用 Azure CLI 登入：

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        因為服務主體可能無法存取訂用帳戶，所以使用 `--allow-no-subscriptions` 引數。

2.  使用環境變數來指定服務主體詳細資料。  如需詳細資訊，請參閱[使用服務主體執行應用程式](#running-the-application-using-a-service-principal)。

當您登入 Azure 之後，`AzureServiceTokenProvider` 會使用服務主體來擷取用於本機開發的權杖。

這只適用於本機開發。 當您將解決方案部署至 Azure 時，程式庫會切換為以受控識別進行驗證。

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>執行應用程式使用受控身分識別或指派給使用者的身分識別 

當您在 Azure App Service 或啟用受控識別的 Azure VM 上執行程式碼時，程式庫會自動使用受控識別。 

或者，您可能會使用指派給使用者的身分識別驗證。 如需有關指派給使用者的身分識別的詳細資訊，請參閱[關於受控身分識別適用於 Azure 資源](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。 若要使用使用者指派的身分識別進行驗證，您需要連接字串中指定的使用者指派身分識別的用戶端識別碼。 中指定的連接字串[連接字串支援](#connection-string-support)下一節。

## <a name="running-the-application-using-a-service-principal"></a>使用服務主體執行應用程式 

這可能需要建立 Azure AD 用戶端認證，才可進行驗證。 常見範例包括：

- 您的程式碼在本機開發環境中執行，但不是在開發人員的身分識別下執行。  例如，Service Fabric 在本機開發上使用 [NetworkService 帳戶](../service-fabric/service-fabric-application-secret-management.md)。
 
- 您的程式碼在本機開發環境中執行，而且向自訂服務進行驗證，因此您無法使用您的開發人員身分識別。 
 
- 您的程式碼會在對 Azure 資源的受控識別尚不支援的 Azure 計算資源上執行，例如 Azure Batch。

有三個主要方法來執行您的應用程式中使用服務主體。 若要使用任何一個，您必須先[建立服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>使用本機金鑰存放區中的憑證，來登入 Azure AD

1. 建立服務主體憑證，使用 Azure CLI [az ad sp 建立-針對-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令。 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    這會建立.pem 檔案 （私密金鑰），將會儲存在您的主目錄。 將此憑證部署至其中一個*LocalMachine*或是*CurrentUser*儲存。 

    > [!Important]
    > CLI 命令所產生.pem 檔案，但 Windows 只提供原生支援 PFX 憑證。 若要改為產生的 PFX 憑證，請使用如下所示的 PowerShell 命令：[使用自我簽署憑證建立服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)。 這些命令會自動部署以及憑證。

1. 將名為 **AzureServicesAuthConnectionString** 的環境變數設定為：

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    使用步驟 1 產生的值取代 *{AppId}* 、 *{TenantId}* 和 *{Thumbprint}* 。 請根據您的部署計劃將 *{CertificateStore}* 取代為 `LocalMachine` 或 `CurrentUser`。

1. 執行應用程式。 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>使用共用密碼認證來登入 Azure AD

1. 使用密碼建立服務主體憑證[az ad sp 建立-針對-rbac-密碼](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)。 

2. 將名為 **AzureServicesAuthConnectionString** 的環境變數設定為：

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    使用步驟 1 產生的值取代 _{AppId}_ 、 _{TenantId}_ 和 _{ClientSecret}_ 。

3. 執行應用程式。 

當所有項目都正確地設定之後，便不必再變更任何程式碼。  `AzureServiceTokenProvider` 會使用環境變數和憑證來向 Azure AD 進行驗證。 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>使用金鑰保存庫中的憑證，來登入 Azure AD

此選項可讓您儲存在 Key Vault 的服務主體的用戶端憑證，並將它用於服務主體驗證。 您可以使用這對下列案例：

* 本機驗證，您想要驗證使用明確的服務主體，並想要安全地保留在金鑰保存庫中的服務主體認證。 開發人員帳戶必須具有金鑰保存庫的存取。 
* 從您想要使用明確的認證 （例如，對於跨租用戶案例中），並想要將服務主體認證安全地保留在金鑰保存庫的 Azure 驗證。 受控身分識別必須具有存取金鑰保存庫。 

受管理的身分識別或您的開發人員身分識別必須具有要擷取的用戶端憑證從 Key Vault 的權限。 AppAuthentication 程式庫會使用擷取的憑證，為服務主體的用戶端認證。

若要使用的服務主體驗證的用戶端憑證

1. 建立服務主體的憑證，並自動將它儲存在金鑰保存庫使用 Azure CLI [az ad sp 建立-針對-rbac-keyvault <keyvaultname> -cert <certificatename> -建立-憑證-略過指派](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令：

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    憑證識別碼將會採用格式的 URL `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. 取代`{KeyVaultCertificateSecretIdentifier}`憑證識別碼與此連接字串中：

```
RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
```

如果您的金鑰保存庫執行個體呼叫 「 myKeyVault"，您建立了稱為 'myCert' 的憑證是憑證識別碼`https://myKeyVault.vault.azure.net/secrets/myCert`，而且連接字串會是`RunAs=App;AppId={TestAppId};TenantId={TenantId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert`。

## <a name="connection-string-support"></a>連接字串支援

依預設，`AzureServiceTokenProvider` 會使用多個方法來擷取權杖。 

若要控制處理程序，請使用傳遞至 `AzureServiceTokenProvider` 建構函式或 *AzureServicesAuthConnectionString* 環境變數中指定的連接字串。 

支援下列選項：

| 連接字串選項 | 案例 | 註解|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | 本機開發 | AzureServiceTokenProvider 會使用 AzureCli 來取得權杖。 |
| `RunAs=Developer; DeveloperTool=VisualStudio` | 本機開發 | AzureServiceTokenProvider 會使用 Visual Studio 來取得權杖。 |
| `RunAs=CurrentUser` | 本機開發 | AzureServiceTokenProvider 會使用 Azure AD 整合式驗證來取得權杖。 |
| `RunAs=App` | [適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider 會使用受控識別取得權杖。 |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [適用於 Azure 資源的使用者指派身分識別](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | Azureservicetokenprovider 會使用指派給使用者的身分識別來取得權杖。 |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | 自訂服務驗證 | KeyVaultCertificateSecretIdentifier = 憑證的密碼識別碼。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| 服務主體 | `AzureServiceTokenProvider` 會使用憑證從 Azure AD 取得權杖。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | 服務主體 | `AzureServiceTokenProvider` 會使用憑證從 Azure AD 取得權杖|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | 服務主體 |`AzureServiceTokenProvider` 會使用密碼從 Azure AD 取得權杖。 |

## <a name="samples"></a>範例

若要查看`Microsoft.Azure.Services.AppAuthentication`文件庫中的動作，請參閱下列程式碼範例。

1. [在執行階段使用受控識別從 Azure Key Vault 擷取秘密](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [使用受控識別以程式設計方式從 Azure VM 部署 Azure Resource Manager 範本](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)。

3. [使用 .NET Core 範例和受控識別從 Azure Linux VM 呼叫 Azure 服務](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/index.yml)。
- 深入了解 [Azure AD 驗證案例](../active-directory/develop/active-directory-authentication-scenarios.md)。
