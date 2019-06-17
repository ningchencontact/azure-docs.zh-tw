---
title: Azure Active Directory 憑證式驗證使用 Azure Cosmos DB
description: 了解如何從 Azure Cosmos DB 存取金鑰設定憑證型驗證的 Azure AD 身分識別。
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: eb8c98df0f015244adf06a9b57f2223509f1f081
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082959"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-account"></a>從 Azure Cosmos 帳戶存取金鑰的 Azure AD 身分識別的憑證型驗證

憑證型驗證可讓用戶端應用程式使用 Azure Active Directory (Azure AD) 進行驗證，以用戶端憑證。 您可以執行憑證型驗證，您需要身分識別，例如內部部署機器或虛擬機器，在 Azure 中的電腦上。 您的應用程式然後可以讀取 Azure Cosmo DB 的索引鍵，而不需要直接在應用程式中的索引鍵。 本文說明如何建立範例 Azure AD 應用程式、 設定它以進行憑證型驗證、 登入 Azure 中使用新的應用程式身分識別，，然後從您的 Azure Cosmos 帳戶時擷取的金鑰。 本文使用 Azure PowerShell 來設定身分識別，並提供C#範例應用程式，驗證，並從您的 Azure Cosmos 帳戶存取金鑰。  

## <a name="prerequisites"></a>必要條件

* 安裝[最新版本](/powershell/azure/install-az-ps)的 Azure PowerShell。

* 如果您沒有 [Azure 訂用帳戶](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="register-an-app-in-azure-ad"></a>在 Azure AD 中註冊應用程式

在此步驟中，您將在您的 Azure AD 帳戶中註冊範例 web 應用程式。 此應用程式是稍後會用來讀取您的 Azure Cosmos 帳戶中的索引鍵。 若要註冊應用程式中使用下列步驟： 

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 Azure **Active Directory**窗格中，移至應用程式註冊 窗格中，並選取**新的註冊**。 

   ![在 Active Directory 中的新應用程式註冊](./media/certificate-based-authentication/new-app-registration.png)

1. 填滿**註冊應用程式**表單詳細資料如下：  

   * **名稱**– 提供您的應用程式的名稱，它可以是任何名稱，例如"範例應用程式 」。
   * **支援的帳戶類型**– 選擇**此組織目錄中只有 （預設目錄） 的帳戶**以便讓您存取此應用程式的目前目錄中的資源。 
   * **重新導向 URL** -選擇應用程式的型別**Web**和提供託管您的應用程式的 URL，則它可以是任何 URL。 針對此範例中，您可以提供測試 URL，例如`https://sampleApp.com`即使應用程式不存在，它是沒問題。

   ![註冊範例 web 應用程式](./media/certificate-based-authentication/register-sample-web-app.png)

1. 選取 **註冊**填滿表單之後。

1. 註冊 app 之後，請記下的**Application(client) 識別碼**並**物件識別碼**，您將在下一個步驟中使用這些詳細資料。 

   ![取得應用程式和物件識別碼](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>安裝 azure Ad 模組

在此步驟中，您將會安裝 Azure AD PowerShell 模組。 此模組，才能取得您在上一個步驟中註冊應用程式的識別碼，並關聯至該應用程式的自我簽署的憑證。 

1. 系統管理員權限開啟 Windows PowerShell ISE。 如果您尚未執行，請安裝 AZ PowerShell 模組，並連接到您訂用帳戶。 如果您有多個訂用帳戶，您可以設定目前的訂用帳戶的內容，如下列命令所示：

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. 安裝並匯入[AzureAD](/powershell/module/azuread/?view=azureadps-2.0)模組

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>登入您的 Azure AD

登入您的 Azure AD 註冊應用程式。 您可以使用 Connect-azuread 命令來登入您的帳戶，在快顯視窗中輸入 Azure 帳號認證。 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>建立自我簽署憑證

開啟另一個執行個體的 Windows PowerShell ISE 中，然後執行下列命令來建立自我簽署的憑證，並讀取與憑證相關聯的索引鍵：

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>建立憑證式認證 

接下來，請執行下列命令來取得您的應用程式的物件識別碼，並建立的憑證為基礎的認證。 在此範例中，我們設定要在一年之後到期的憑證，您可以將它設定在任何需要的結束日期。

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

上述命令會產生類似以下螢幕擷取畫面的輸出：

![憑證型認證建立輸出](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>設定您的 Azure Cosmos 帳戶，以使用新的身分識別

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 瀏覽至您的 Azure Cosmos 帳戶，開啟**存取控制 (IAM)** 刀鋒視窗。

1. 選取 **新增**並**新增角色指派**。 新增您在上一個步驟中建立 sampleApp**參與者**角色，如下列螢幕擷取畫面所示：

   ![設定 Azure Cosmos 帳戶，以便使用新的身分識別](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. 選取 **儲存**填妥表單後


## <a name="access-the-keys-from-powershell"></a>從 PowerShell 存取金鑰

在此步驟中，您會使用應用程式和您建立並存取您的 Azure Cosmos 帳戶金鑰的憑證來登入 Azure。 

1. 一開始，請清除您用來登入您的帳戶的 Azure 帳戶的認證。 您可以使用下列命令來清除認證：

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. 接下來驗證，您可以使用應用程式的認證登入 Azure 入口網站，並存取 Azure Cosmos DB 金鑰：

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

前一個命令會顯示您的 Azure Cosmos 帳戶的主要和次要的主要金鑰。 您可以檢視您的 Azure Cosmos 帳戶，以驗證 get 索引鍵要求成功，以及事件起始者為"範例應用程式 」 應用程式的活動記錄。 
 
![驗證 Azure AD 中的 get 索引鍵呼叫](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>存取金鑰，從C#應用程式 

您也可以藉由存取金鑰來驗證此案例中C#應用程式。 下列C#主控台應用程式，可使用 Active Directory 中註冊的應用程式存取 Azure Cosmos DB 的索引鍵。 請務必更新 tenantId、 clientID、 certName、 資源群組名稱、 訂用帳戶 ID，Azure Cosmos 帳戶名稱詳細資料，然後再執行程式碼。 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

此指令碼輸出，主要和次要的主要金鑰，如下列螢幕擷取畫面所示：

![csharp 應用程式輸出](./media/certificate-based-authentication/csharp-application-output.png)

類似於上一節，您可以檢視您的 Azure Cosmos 帳戶，以驗證 get 索引鍵要求事件由"範例應用程式 」 應用程式的活動記錄。 


## <a name="next-steps"></a>後續步驟

* [保護使用 Azure Key Vault 的 Azure Cosmos 金鑰](access-secrets-from-keyvault.md)

* [Azure Cosmos DB 的安全性屬性](cosmos-db-security-attributes.md)