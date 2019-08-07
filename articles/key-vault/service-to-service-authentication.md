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
ms.openlocfilehash: d34c94ccca47d29afc4f3d83bec58db737be270c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840409"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>使用 .NET 進行 Azure Key Vault 的服務對服務驗證

若要驗證 Azure Key Vault 您需要一個 Azure Active Directory (AD) 認證, 也就是共用密碼或憑證。 

管理這類認證並不簡單，而且我們很容易會將認證包含在來源或設定檔中，藉此與應用程式結合在一起。  .NET 程式庫的 `Microsoft.Azure.Services.AppAuthentication` 可簡化此問題。 它會使用開發人員的認證，在本機開發期間進行驗證。 當解決方案在稍後部署至 Azure 時，程式庫會自動切換至應用程式認證。    在本機開發期間使用開發人員認證會比較安全，因為您不需要建立 Azure AD 認證，或在開發人員之間共用認證。

`Microsoft.Azure.Services.AppAuthentication` 程式庫會自動管理驗證，這可讓您將焦點放在您的方案，而不是認證上。  它支援 Microsoft Visual Studio、Azure CLI 或 Azure AD 整合式驗證的本機開發。 如果部署到支援受控執行個體的 Azure 資源，程式庫會自動使用 [Azure 資源的受控識別](../active-directory/msi-overview.md)。 不需要任何程式碼或設定變更。 當受控識別無法使用，或在本機開發期間無法判斷開發人員的安全性內容時，程式庫也支援直接使用 Azure AD [用戶端認證](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

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

針對本機開發, 有兩個主要的驗證案例:[向 Azure 服務進行驗證](#authenticating-to-azure-services), 以及[對自訂服務進行驗證](#authenticating-to-custom-services)。

### <a name="authenticating-to-azure-services"></a>向 Azure 服務驗證

本機電腦不支援 Azure 資源的受控識別。  因此，`Microsoft.Azure.Services.AppAuthentication` 程式庫會使用您的開發人員認證，以在本機開發環境中執行。 當解決方案部署至 Azure 時，程式庫會使用受控識別切換至 OAuth 2.0 用戶端的認證授與流程。  這表示您可以放心地在本機或遠端測試相同程式碼。

針對本機開發，`AzureServiceTokenProvider` 會使用 **Visual Studio**、**Azure 命令列介面** (CLI)，或 **Azure AD 整合式驗證**來擷取權杖。 會依序嘗試每個選項，而程式庫會使用第一個成功的選項。 如果沒有選項可用，`AzureServiceTokenProviderException` 會擲回包含詳細資訊的例外狀況。

### <a name="authenticating-with-visual-studio"></a>使用 Visual Studio 進行驗證

使用 Visual Studio 進行驗證具有下列必要條件:

1. [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/)或更新版本。

2. [Visual Studio 的應用程式驗證延伸](https://go.microsoft.com/fwlink/?linkid=862354)模組, 可作為 Visual Studio 2017 update 5 的個別延伸模組, 並隨附于 Update 6 和更新版本中的產品。 使用 Update 6 或更新版本, 您可以從 Visual Studio 安裝程式中選取 Azure 開發工具, 以確認應用程式驗證延伸模組的安裝。
 
登入 Visual Studio 並使用 [**工具**&nbsp; > &nbsp;] [**選項**&nbsp; > ]&nbsp;[**Azure 服務驗證**] 選取帳戶以進行本機開發。 

如果您使用 Visual Studio 時遇到問題，例如發生有關權杖提供者檔案的錯誤，請仔細檢閱這些步驟。 

此時也可能需要重新驗證您的開發人員權杖。 若要這樣做，請移至 [工具]&nbsp;>&nbsp;[選項]>[Azure&nbsp;服務驗證]，然後在選取的帳戶下尋找 [重新驗證] 連結。&nbsp;  選取該項目並進行驗證。 

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

### <a name="authenticating-with-azure-ad-authentication"></a>使用 Azure AD 驗證進行驗證

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

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>使用受控識別或使用者指派的身分識別來執行應用程式 

當您在 Azure App Service 或啟用受控識別的 Azure VM 上執行程式碼時，程式庫會自動使用受控識別。 

或者, 您可以使用使用者指派的身分識別來進行驗證。 如需使用者指派身分識別的詳細資訊, 請參閱[關於 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)識別。 若要使用使用者指派的身分識別進行驗證, 您需要在連接字串中指定使用者指派之身分識別的用戶端識別碼。 連接字串是在下面的[連接字串支援](#connection-string-support)一節中指定。

## <a name="running-the-application-using-a-service-principal"></a>使用服務主體執行應用程式 

這可能需要建立 Azure AD 用戶端認證，才可進行驗證。 常見範例包括：

- 您的程式碼在本機開發環境中執行，但不是在開發人員的身分識別下執行。  例如，Service Fabric 在本機開發上使用 [NetworkService 帳戶](../service-fabric/service-fabric-application-secret-management.md)。
 
- 您的程式碼在本機開發環境中執行，而且向自訂服務進行驗證，因此您無法使用您的開發人員身分識別。 
 
- 您的程式碼會在對 Azure 資源的受控識別尚不支援的 Azure 計算資源上執行，例如 Azure Batch。

使用服務主體來執行應用程式的主要方法有三種。 若要使用其中任何一項, 您必須先[建立服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>使用本機密鑰存放區中的憑證登入 Azure AD

1. 使用 Azure CLI [az ad sp create-rbac 命令來](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)建立服務主體憑證。 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    這會建立將儲存在主目錄中的 pem 檔案 (私密金鑰)。 請將此憑證部署至*LocalMachine*或*CurrentUser*存放區。 

    > [!Important]
    > CLI 命令會產生一個 pem 檔案, 但 Windows 只會提供 PFX 憑證的原生支援。 若要改為產生 PFX 憑證, 請使用如下所示的 PowerShell 命令:[使用自我簽署憑證建立服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)。 這些命令也會自動部署憑證。

1. 將名為 **AzureServicesAuthConnectionString** 的環境變數設定為：

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    使用步驟 1 產生的值取代 *{AppId}* 、 *{TenantId}* 和 *{Thumbprint}* 。 請根據您的部署計劃將 *{CertificateStore}* 取代為 `LocalMachine` 或 `CurrentUser`。

1. 執行應用程式。 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>使用共用密碼認證來登入 Azure AD

1. 使用[az ad sp create---password 來](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)建立具有密碼的服務主體憑證。 

2. 將名為 **AzureServicesAuthConnectionString** 的環境變數設定為：

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    使用步驟 1 產生的值取代 _{AppId}_ 、 _{TenantId}_ 和 _{ClientSecret}_ 。

3. 執行應用程式。 

當所有項目都正確地設定之後，便不必再變更任何程式碼。  `AzureServiceTokenProvider` 會使用環境變數和憑證來向 Azure AD 進行驗證。 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>使用 Key Vault 中的憑證登入 Azure AD

此選項可讓您將服務主體的用戶端憑證儲存在 Key Vault 中, 並用於服務主體驗證。 在下列案例中, 您可以使用此項:

* 本機驗證, 您想要使用明確的服務主體進行驗證, 而且想要在金鑰保存庫中安全地保留服務主體認證。 開發人員帳戶必須擁有金鑰保存庫的存取權。 
* 從 Azure 驗證您想要使用明確認證的位置 (例如, 用於跨租使用者案例), 並想要在金鑰保存庫中安全地保留服務主體認證。 受控識別必須具有金鑰保存庫的存取權。 

受控識別或您的開發人員身分識別必須具有從 Key Vault 取得用戶端憑證的許可權。 AppAuthentication 程式庫會使用抓取的憑證做為服務主體的用戶端認證。

若要使用用戶端憑證進行服務主體驗證

1. 建立服務主體憑證, 並使用 Azure CLI [az ad sp 建立----keyvault <keyvaultname> --cert <certificatename> ----](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) ----------------------------

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    憑證識別碼會是格式的 URL`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. 在`{KeyVaultCertificateSecretIdentifier}`此連接字串中, 以憑證識別碼取代:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    例如, 如果您的金鑰保存庫稱為「myKeyVault」, 而且您已建立名為 ' Mycert.cer ' 的憑證, 則憑證識別碼會是:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```


## <a name="connection-string-support"></a>連接字串支援

依預設，`AzureServiceTokenProvider` 會使用多個方法來擷取權杖。 

若要控制處理程序，請使用傳遞至 `AzureServiceTokenProvider` 建構函式或 *AzureServicesAuthConnectionString* 環境變數中指定的連接字串。 

支援下列選項：

| 連接字串選項 | 狀況 | 註解|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | 本機開發 | AzureServiceTokenProvider 會使用 AzureCli 來取得權杖。 |
| `RunAs=Developer; DeveloperTool=VisualStudio` | 本機開發 | AzureServiceTokenProvider 會使用 Visual Studio 來取得權杖。 |
| `RunAs=CurrentUser` | 本機開發 | AzureServiceTokenProvider 會使用 Azure AD 整合式驗證來取得權杖。 |
| `RunAs=App` | [適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider 會使用受控識別取得權杖。 |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [適用于 Azure 資源的使用者指派身分識別](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | Azureservicetokenprovider 會會使用使用者指派的身分識別來取得權杖。 |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | 自訂服務驗證 | KeyVaultCertificateSecretIdentifier = 憑證的秘密識別碼。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| 服務主體 | `AzureServiceTokenProvider` 會使用憑證從 Azure AD 取得權杖。 |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | 服務主體 | `AzureServiceTokenProvider` 會使用憑證從 Azure AD 取得權杖|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | 服務主體 |`AzureServiceTokenProvider` 會使用密碼從 Azure AD 取得權杖。 |

## <a name="samples"></a>範例

若要查看`Microsoft.Azure.Services.AppAuthentication`作用中的程式庫, 請參閱下列程式碼範例。

1. [在執行階段使用受控識別從 Azure Key Vault 擷取秘密](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [使用受控識別以程式設計方式從 Azure VM 部署 Azure Resource Manager 範本](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)。

3. [使用 .NET Core 範例和受控識別從 Azure Linux VM 呼叫 Azure 服務](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)。

## <a name="appauthentication-troubleshooting"></a>AppAuthentication 疑難排解

### <a name="common-issues-during-local-development"></a>本機開發期間常見的問題

#### <a name="azure-cli-is-not-installed-you-are-not-logged-in-or-you-do-not-have-the-latest-version"></a>未安裝 Azure CLI、您未登入, 或您沒有最新版本

執行**az account get-存取權杖**, 以查看 Azure CLI 是否會為您顯示權杖。 如果找不到這類程式, 請安裝[最新版本的 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 如果您已安裝它, 系統可能會提示您登入。 
 
#### <a name="azureservicetokenprovider-cannot-find-the-path-for-azure-cli"></a>Azureservicetokenprovider 會找不到 Azure CLI 的路徑

Azureservicetokenprovider 會會在其預設安裝位置尋找 Azure CLI。 如果找不到 Azure CLI, 請將環境變數**AzureCLIPath**設定為 Azure CLI 安裝資料夾。 Azureservicetokenprovider 會會將環境變數加入 Path 環境變數中。
 
#### <a name="you-are-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>您已使用多個帳戶登入 Azure CLI, 相同的帳戶可存取多個租使用者中的訂用帳戶, 或者您在本機開發期間嘗試進行呼叫時, 收到拒絕存取的錯誤

使用 Azure CLI, 將預設訂用帳戶設定為具有您想要使用之帳戶的訂閱, 而且位於與您想要存取之資源相同的租使用者中: **az account set--訂用帳戶 [訂用帳戶識別碼]** 。 如果看不到任何輸出, 則會成功。 使用**az account list**確認正確的帳戶現在是預設值。

### <a name="common-issues-across-environments"></a>跨環境的常見問題

#### <a name="unauthorized-access-access-denied-forbidden-etc-error"></a>未經授權的存取、拒絕存取、禁止等錯誤
 
使用的主體無法存取其嘗試存取的資源。 根據您是在本機開發電腦上執行範例, 或是在 Azure 中部署至您的 App Service, 授與您的使用者帳戶或 App Service 的 MSI 「參與者」存取所需的資源。 有些資源 (例如金鑰保存庫) 也有自己的[存取](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies)原則, 您可以使用它們來授與存取權給主體 (使用者、應用程式、群組等)。

### <a name="common-issues-when-deployed-to-azure-app-service"></a>部署到 Azure App Service 時常見的問題

#### <a name="managed-identity-is-not-setup-on-the-app-service"></a>未在 App Service 上設定受控識別
 
使用[Kudu debug console](https://azure.microsoft.com/en-us/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)檢查環境變數 MSI_ENDPOINT 和 MSI_SECRET 存在。 如果這些環境變數不存在, App Service 上就不會啟用受控識別。 
 
### <a name="common-issues-when-deployed-locally-with-iis"></a>使用 IIS 在本機部署時的常見問題

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>在 IIS 中偵測應用程式時, 無法取得權杖

根據預設, AppAuth 會在 IIS 中的不同使用者內容中執行, 因此不具有使用您的開發人員身分識別來抓取存取權杖的存取權。 您可以使用下列兩個步驟, 將 IIS 設定為與您的使用者內容一起執行:
- 設定應用程式集區, 讓 web 應用程式以您目前的使用者帳戶身分執行。 請在[這裡](https://docs.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)參閱詳細資訊
- 將 "setProfileEnvironment" 設定為 "True"。 請參閱[這裡](https://docs.microsoft.com/en-us/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)的詳細資訊。 

    - 前往%windir%\System32\inetsrv\config\applicationHost.config
    - 搜尋 "setProfileEnvironment"。 如果設定為 "False", 請將它變更為 "True"。 如果不存在, 請將它新增為 processModel 元素的屬性 (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment), 並將它設定為 "True"。

- 深入了解 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/index.yml)。
- 深入了解 [Azure AD 驗證案例](../active-directory/develop/active-directory-authentication-scenarios.md)。
