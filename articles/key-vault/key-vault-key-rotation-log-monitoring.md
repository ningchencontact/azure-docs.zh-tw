---
title: 使用端對端金鑰輪替和稽核設定 Azure 金鑰保存庫 | Microsoft Docs
description: 使用本作法指南，協助您設定金鑰輪替和監視金鑰保存庫記錄檔。
services: key-vault
author: barclayn
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 20a170963ff4a8ff9cb69d3397e66e12c1047d16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65561194"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>使用金鑰輪替和稽核設定 Azure Key Vault

## <a name="introduction"></a>簡介

當您具有金鑰保存庫之後，即可開始用它來儲存金鑰和秘密。 應用程式不再需要保存您的金鑰或秘密，而是可視需要從保存庫要求取得。 金鑰保存庫可讓您更新金鑰和密碼，而不會影響您的應用程式，有各種可能您的金鑰和祕密管理開啟的行為。

>[!IMPORTANT]
> 本文中的範例僅供說明之用。 它們被不適用於生產環境中使用。 

本文將逐步解說：

- 使用 Azure Key Vault 來儲存秘密的範例。 在本文中，儲存的密碼會是應用程式所存取的 Azure 儲存體帳戶金鑰。 
- 如何實作該儲存體帳戶金鑰的排程的輪替。
- 如何監視金鑰保存庫稽核記錄檔，並提出未預期的要求時發出警示。

> [!NOTE]
> 金鑰保存庫的初始設定時，不會詳細解釋這篇文章。 如需此資訊，請參閱[什麼是 Azure Key Vault？](key-vault-overview.md)。 跨平台命令列介面指示，請參閱[使用 Azure CLI 管理 Key Vault](key-vault-manage-with-cli2.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>設定金鑰保存庫

若要讓應用程式能夠從金鑰保存庫擷取密碼，您必須先建立此密碼，並上傳至保存庫。

開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：

```powershell
Connect-AzAccount
```

在快顯瀏覽器視窗中，輸入您的 Azure 帳戶的使用者名稱和密碼。 PowerShell 會取得與此帳戶相關聯的所有訂用帳戶。 PowerShell 使用預設第一個訂用帳戶。

如果您有多個訂用帳戶，您可能必須指定用來建立金鑰保存庫的那一個訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzSubscription
```

若要指定將會記錄您的金鑰保存庫相關聯的訂用帳戶，請輸入：

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

因為本文會示範如何將儲存體帳戶金鑰儲存為密碼，所以您必須取得該儲存體帳戶金鑰。

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

擷取您的密碼 （在此案例中，您的儲存體帳戶金鑰） 之後, 您必須將該索引鍵轉換成安全字串，然後使用該值在金鑰保存庫中建立密碼。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

接下來，取得您建立之密碼的 URI。 您將需要此 URI 在稍後的步驟以呼叫金鑰保存庫並擷取您的密碼。 執行下列 PowerShell 命令，並記下 ID 值，這是祕密的 URI:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>設定範例應用程式

已儲存的密碼之後，您可以使用程式碼來擷取並使用執行幾個步驟之後。

首先，您必須註冊您的應用程式與 Azure Active Directory。 然後告訴 Key Vault 應用程式的資訊，讓它可以允許來自您的應用程式的要求。

> [!NOTE]
> 應用程式必須建立在與金鑰保存庫相同的 Azure Active Directory 租用戶上。

1. 開啟**Azure Active Directory**。
2. 選取 [應用程式註冊]  。 
3. 選取 **新的應用程式註冊**新增至 Azure Active Directory 應用程式。

    ![在 Azure Active Directory 中開啟應用程式](./media/keyvault-keyrotation/azure-ad-application.png)

4. 底下**Create**，讓應用程式類型保持**Web 應用程式 / API**並提供您的應用程式的名稱。 提供您的應用程式**登入 URL**。 此 URL 可以是任何您想用於此示範的項目。

    ![建立應用程式註冊](./media/keyvault-keyrotation/create-app.png)

5. 應用程式加入 Azure Active Directory 之後，就會開啟應用程式頁面。 選取 **設定**，然後選取**屬性**。 複製 [應用程式識別碼]  值。 您將在稍後步驟中需要它。

接下來，產生您的應用程式的金鑰，因此它可以與 Azure Active Directory 互動。 若要建立金鑰，請選取**金鑰**下方**設定**。 記下您的 Azure Active Directory 應用程式新產生的金鑰。 您在後續的步驟中將需要此資訊。 離開此區段之後，無法使用索引鍵。 

![Azure Active Directory 應用程式金鑰](./media/keyvault-keyrotation/create-key.png)

建立您的應用程式至金鑰保存庫中的任何呼叫之前，您必須讓金鑰保存庫，有關您的應用程式和其權限。 下列命令來授與應用程式使用保存庫名稱和您的 Azure Active Directory 應用程式的應用程式識別碼**取得**存取您金鑰保存庫。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

您現在準備好開始建置應用程式呼叫。 在您的應用程式，您必須安裝與 Azure 金鑰保存庫和 Azure Active Directory 互動所需的 NuGet 套件。 從 Visual Studio Package Manager Console 輸入下列命令。 在撰寫本文時，Azure Active Directory 套件的目前版本是 3.10.305231913，因此請確認最新的版本和更新所需。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在應用程式程式碼中，建立類別以保有Azure Active Directory 驗證的方法。 在此範例中，該類別稱為 **Utils**。 加入下列 `using` 陳述式：

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接著，新增下列方法，以從 Azure Active Directory 擷取 JWT 權杖。 為了能夠維護，您可能想要將硬式編碼的字串值移動至 web 或應用程式組態。

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

新增必要的程式碼，以呼叫金鑰保存庫並擷取密碼值。 首先，您必須新增下列`using`陳述式：

```csharp
using Microsoft.Azure.KeyVault;
```

新增方法呼叫來叫用金鑰保存庫，並擷取密碼。 在這個方法中，您會提供您在先前步驟中儲存的密碼 URI。 請注意，使用**GetToken**方法，從**Utils**您先前建立的類別。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

當您執行應用程式時，您現在應該向 Azure Active Directory 進行驗證，然後從 Azure 金鑰保存庫中擷取密碼值。

## <a name="key-rotation-using-azure-automation"></a>使用 Azure 自動化的金鑰輪替

> [!IMPORTANT]
> Azure 自動化 runbook 仍需要使用`AzureRM`模組。

您現在已準備好設定其輪替策略，您將儲存為 Key Vault 祕密的值。 以數種方式可以旋轉祕密：

- 手動程序的一部分
- 以程式設計方式使用 API 呼叫
- 透過 Azure 自動化指令碼

基於本文的目的，您將使用 PowerShell 結合 Azure 自動化，以變更 Azure 儲存體帳戶的存取金鑰。 然後，您就會使用這個新金鑰更新金鑰保存庫密碼。

若要讓 Azure 自動化在金鑰保存庫中設定密碼值，您必須取得的用戶端識別碼，名為的連線**AzureRunAsConnection**。 建立您的 Azure 自動化執行個體時，已建立此連線。 若要尋找此識別碼，請選取**資產**從您的 Azure 自動化執行個體。 從該處選取**連線**，然後選取**AzureRunAsConnection**服務主體。 請記下的**ApplicationId**值。

![Azure 自動化用戶端識別碼](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在 **資產**，選取**模組**。 選取 **資源庫**，然後搜尋並匯入下列模組的每個更新的版本：

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 在撰寫本文的當時，就下列指令碼而言，需要更新的只有先前所述的模組。 如果您的自動化作業失敗，請確認您已匯入所有必要的模組和其相依性。

您已擷取您的 Azure 自動化連線的應用程式識別碼之後，您必須告訴您金鑰保存庫，此應用程式已更新您的保存庫中的祕密權限。 使用下列 PowerShell 命令：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下來，選取**Runbook**下方，您的 Azure 自動化執行個體，然後選取**新增 Runbook**。 選取 [快速建立]  。 命名您的 runbook，然後選取**PowerShell**做為 runbook 類型。 您可以新增描述。 最後，選取 [建立]  。

![建立 Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

在新的 Runbook 的編輯器窗格中，貼上下列 PowerShell 指令碼：

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

在 [編輯器] 窗格中，選取**測試窗格**來測試您的指令碼。 指令碼執行時不會發生錯誤之後，您可以選取**發佈**，然後您可以在其中套用 runbook 的組態窗格中的 runbook 的排程。

## <a name="key-vault-auditing-pipeline"></a>金鑰保存庫稽核管線

當您設定金鑰保存庫時，您可以開啟稽核功能，以收集對金鑰保存庫提出的存取要求的記錄。 這些記錄檔會儲存在指定的 Azure 儲存體帳戶，並可供提取、 監視和分析。 下列案例會使用 Azure functions、 Azure logic apps 和金鑰保存庫稽核記錄檔來建立管線時，會傳送一封電子郵件不符合 web 應用程式的應用程式識別碼的應用程式會從保存庫擷取祕密。

首先，您必須在金鑰保存庫上啟用記錄功能。 使用下列 PowerShell 命令。 (您可以看到在的完整詳細資料[這篇文章有關金鑰保存庫記錄](key-vault-logging.md)。)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

啟用記錄之後，稽核記錄檔就會開始儲存在指定的儲存體帳戶中。 這些記錄包含有關金鑰保存庫存取方式、時間和存取者的事件。

> [!NOTE]
> 在金鑰保存庫作業 10 分鐘後，您就可以存取記錄資訊。 它通常會提供比，更快。

下一步是 [建立 Azure 服務匯流排佇列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。 此佇列是金鑰保存庫稽核記錄檔的推送位置。 在佇列中的稽核記錄檔訊息時，邏輯應用程式加以挑選並採取行動。 建立服務匯流排執行個體，執行下列步驟：

1. （如果您已經有一個您想要使用，請跳至步驟 2），請建立服務匯流排命名空間。
2. 瀏覽至服務匯流排執行個體，在 Azure 入口網站中，選取您想要在其中建立佇列的命名空間。
3. 選取 建立資源  、 > 企業整合  、服務匯流排 > ，然後輸入必要的詳細資料。 
4. 尋找選取命名空間，然後選取服務匯流排連接資訊**連接資訊**。 您將需要此資訊在下一節。

下一步[建立 Azure 函式](../azure-functions/functions-create-first-azure-function.md)以輪詢儲存體帳戶內的金鑰保存庫記錄檔，並挑選新的事件。 依排程，就會觸發此函式。

若要建立 Azure 函數應用程式，請選取**建立資源**，在 marketplace 內搜尋**函式應用程式**，然後選取**建立**。 在建立期間，您可以使用現有的主控方案，或建立新的方案。 您也可以選擇動態主控。 如需 Azure Functions 的裝載選項的詳細資訊，請參閱[如何調整 Azure Functions](../azure-functions/functions-scale.md)。

建立 Azure 函數應用程式之後，請移至它，然後選取**計時器**案例並**C\#** 語言。 然後選取**建立此函式**。

![Azure Functions 啟動刀鋒視窗](./media/keyvault-keyrotation/Azure_Functions_Start.png)

在 [開發]  索引標籤上，以下列內容取代 run.csx 程式碼︰

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> 變更的變數前面的程式碼，以指向您儲存體帳戶的金鑰保存庫記錄檔寫入的位置時，服務匯流排執行個體來建立更早版本，以及指向金鑰保存庫儲存體記錄的特定路徑。

此函式會挑選金鑰保存庫記錄所寫入到的儲存體帳戶中最新的記錄、取得來自該檔案的最新事件，並推送到服務匯流排佇列。 

由於單一檔案可以有多個事件，您應該建立函式也會查看以判斷所挑選的最後一個事件的時間戳記的 sync.txt 檔案。 使用此檔案，可確保，您不會推送相同事件多次。 

Sync.txt 檔案包含最後發生的事件時間戳記。 當載入記錄檔時，它們必須排序根據其時間戳記，以確保它們正在正確排序。

為此函式，我們會參考一些額外的程式庫不是在 Azure Functions 中立即可用的。 若要包含這些程式庫，我們需要 Azure Functions 來使用 NuGet 提取它們。 底下**程式碼**方塊中，選取**檢視檔案**。

![[檢視檔案] 選項](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

加入檔案，名為 project.json，使用下列內容：

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

選取後**儲存**，Azure Functions 會下載必要的二進位檔。

切換至 [整合]  索引標籤，然後為計時器參數提供有意義的名稱以在函式內使用。 在上述程式碼中，函式預期呼叫計時器*myTimer*。 指定[CRON 運算式](../app-service/webjobs-create.md#CreateScheduledCRON)計時器，如下所示： `0 * * * * *`。 這個運算式會導致函式執行一次一分鐘的時間。

在同一個**整合**索引標籤上，新增類型的輸入**Azure Blob 儲存體**。 此輸入會指向包含函式所查看的最後一個事件的時間戳記的 sync.txt 檔案。 此輸入將存取函式中，使用參數名稱。 在上述程式碼中，Azure Blob 儲存體輸入預期參數名稱為*inputBlob*。 選取儲存體帳戶的 sync.txt 檔案將位於何處 （可能是相同或不同的儲存體帳戶）。 在 [路徑] 欄位中，提供格式檔案的路徑`{container-name}/path/to/sync.txt`。

新增類型的輸出**Azure Blob 儲存體**。 此輸出會指向您在輸入中定義的 sync.txt 檔案。 函式會使用此輸出寫入查看的最後一個事件的時間戳記。 前面的程式碼預期此參數名稱為 outputBlob  。

此函式已準備好。 請務必切換回 [開發]  索引標籤並儲存程式碼。 檢查有任何編譯錯誤 [輸出] 視窗，並視需要加以更正。 如果程式碼會編譯，然後程式碼應該現在會每隔一分鐘檢查金鑰保存庫記錄檔並將任何新事件推送至定義的服務匯流排佇列。 您應該會在每次觸發函式時看到記錄資訊寫入到 [記錄檔] 視窗。

### <a name="azure-logic-app"></a>Azure 邏輯應用程式

接下來，您必須建立 Azure 邏輯應用程式，以挑選函式推送至服務匯流排佇列、 剖析內容，並傳送電子郵件符合的條件為基礎的事件。

[建立 logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)藉由選取**建立資源** > **整合** > **邏輯應用程式**。

建立邏輯應用程式之後，請移至它，然後選取**編輯**。 在邏輯應用程式編輯器中，選取**服務匯流排佇列**並輸入您的服務匯流排認證以連接至佇列。

![Azure 邏輯應用程式服務匯流排](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

選取 [新增條件]  。 在條件中，切換到進階編輯器，並輸入下列程式碼。 取代*APP_ID*實際的應用程式識別碼，為您的 web 應用程式：

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

這個運算式基本上會傳回**假**如果*appid*從內送事件 （這是服務匯流排訊息的主體） 不*appid*應用程式。

現在，底下建立動作**如果否，不要執行任何**。

![Azure 邏輯應用程式選擇動作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

動作中，選取**Office 365-傳送電子郵件**。 填寫欄位，以建立定義的條件傳回 **false** 時要傳送的電子郵件。 如果您沒有 Office 365，尋找替代項目來達到相同的結果。

您現在有新的金鑰保存庫稽核記錄檔尋找一分鐘的端對端管線。 它會將它找到新的記錄檔推送至服務匯流排佇列中。 新訊息放入佇列時就會觸發邏輯應用程式。 如果*appid*內事件的呼叫端的應用程式的應用程式識別碼不符，則會傳送一封電子郵件。
