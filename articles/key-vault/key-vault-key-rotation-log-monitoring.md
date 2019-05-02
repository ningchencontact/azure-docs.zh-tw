---
title: 使用端對端金鑰輪替和稽核設定 Azure 金鑰保存庫 | Microsoft Docs
description: 借助本操作指南设置密钥轮换和监视 Key Vault 日志。
services: key-vault
author: barclayn
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 785e60ddf54a3772ae7687b9d18477ef04707609
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713691"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>使用金鑰輪替和稽核設定 Azure Key Vault

## <a name="introduction"></a>簡介

當您具有金鑰保存庫之後，即可開始用它來儲存金鑰和秘密。 應用程式不再需要保存您的金鑰或秘密，而是可視需要從保存庫要求取得。 使用 Key Vault 可以更新密钥和机密，而不会影响应用程序，同时可以各种可能的方法管理密钥和机密。

>[!IMPORTANT]
> 本文中的範例僅供說明之用。 不应在生产环境中使用。 

本文將逐步解說：

- 使用 Azure Key Vault 來儲存秘密的範例。 在本文中，存储的机密是应用程序访问的 Azure 存储帐户密钥。 
- 如何实现该存储帐户机密的计划轮换。
- 如何监视 Key Vault 审核日志，并在收到意外的请求时发出警报。

> [!NOTE]
> 本文不详细说明 Key Vault 的初始设置。 如需此資訊，請參閱[什麼是 Azure Key Vault？](key-vault-overview.md)。 有关跨平台命令行接口的说明，请参阅[使用 Azure CLI 管理 Key Vault](key-vault-manage-with-cli2.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>設定金鑰保存庫

若要讓應用程式能夠從金鑰保存庫擷取密碼，您必須先建立此密碼，並上傳至保存庫。

開始 Azure PowerShell 工作階段，並使用下列命令登入您的 Azure 帳戶：

```powershell
Connect-AzAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户的用户名和密码。 PowerShell 會取得與此帳戶相關聯的所有訂用帳戶。 PowerShell 使用預設第一個訂用帳戶。

如果您有多個訂用帳戶，您可能必須指定用來建立金鑰保存庫的那一個訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzSubscription
```

若要指定与要记录的 Key Vault 关联的订阅，请输入：

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

因為本文會示範如何將儲存體帳戶金鑰儲存為密碼，所以您必須取得該儲存體帳戶金鑰。

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

检索用户的机密（在本例中，为存储帐户密钥）后，必须将该密钥转换为安全字符串，并在 Key Vault 中使用该值创建机密。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

接下來，取得您建立之密碼的 URI。 在稍后的步骤中调用 Key Vault 和检索机密时，需要用到此 URI。 运行以下 PowerShell 命令，并记下 ID 值（即机密 URI）：

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>設定範例應用程式

存储机密后，可以在执行几个附加步骤后，使用代码检索并使用该机密。

首先必须将应用程序注册到 Azure Active Directory。 然后向 Key Vault 告知应用程序信息，使其允许来自应用程序的请求。

> [!NOTE]
> 應用程式必須建立在與金鑰保存庫相同的 Azure Active Directory 租用戶上。

1. 打开“Azure Active Directory”。
2. 選取 [應用程式註冊]。 
3. 选择“新建应用程序注册”，以将一个应用程序添加到 Azure Active Directory。

    ![在 Azure Active Directory 中開啟應用程式](./media/keyvault-keyrotation/azure-ad-application.png)

4. 在“创建”下，将应用程序类型保留为“Web 应用/API”，并为应用程序命名。 为应用程序指定“登录 URL”。 此 URL 可以是任意 URL，适合本演示即可。

    ![建立應用程式註冊](./media/keyvault-keyrotation/create-app.png)

5. 将应用程序添加到 Azure Active Directory 后，应用程序页将会打开。 依次选择“设置”、“属性”。 複製 [應用程式識別碼] 值。 后面的步骤需要用到。

接下来，为应用程序生成密钥，使其可与 Azure Active Directory 交互。 若要创建密钥，请在“设置”下选择“密钥”。 记下为 Azure Active Directory 应用程序生成的新密钥。 您在後續的步驟中將需要此資訊。 从此部分导航出来以后，该密钥将不可用。 

![Azure Active Directory 应用密钥](./media/keyvault-keyrotation/create-key.png)

在建立从应用程序到 Key Vault 的任何调用之前，必须让 Key Vault 知道应用程序及其权限。 以下命令使用 Azure Active Directory 应用中的保管库名称和应用程序 ID 为应用程序授予对 Key Vault 的 **Get** 访问权限。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

现在可以开始生成应用程序调用。 在应用程序中，必须安装所需的 NuGet 包，以便与 Azure Key Vault 和 Azure Active Directory 交互。 從 Visual Studio Package Manager Console 輸入下列命令。 在编写本文时，Azure Active Directory 包的最新版本为 3.10.305231913，请确认最新版本并视需要进行更新。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在應用程式程式碼中，建立類別以保有Azure Active Directory 驗證的方法。 在此範例中，該類別稱為 **Utils**。 加入下列 `using` 陳述式：

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接著，新增下列方法，以從 Azure Active Directory 擷取 JWT 權杖。 为了方便维护，请将硬编码的字符串值移到 Web 或应用程序配置。

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

新增必要的程式碼，以呼叫金鑰保存庫並擷取密碼值。 首先，必须添加以下 `using` 语句：

```csharp
using Microsoft.Azure.KeyVault;
```

新增方法呼叫來叫用金鑰保存庫，並擷取密碼。 在這個方法中，您會提供您在先前步驟中儲存的密碼 URI。 请注意如何使用前面创建的 **Utils** 类中的 **GetToken** 方法。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

當您執行應用程式時，您現在應該向 Azure Active Directory 進行驗證，然後從 Azure 金鑰保存庫中擷取密碼值。

## <a name="key-rotation-using-azure-automation"></a>使用 Azure 自動化的金鑰輪替

> [!IMPORTANT]
> Azure 自动化 Runbook 仍需使用 `AzureRM` 模块。

现在，对于存储为 Key Vault 机密的值，可以设置轮换策略。 可通过多种方式轮换机密：

- 手动轮换
- 使用 API 调用以编程方式轮换
- 通过 Azure 自动化脚本轮换

本文结合使用 Azure PowerShell 和 Azure 自动化来更改 Azure 存储帐户的访问密钥。 然后使用新密钥更新 Key Vault 机密。

若要允许 Azure 自动化在 Key Vault 中设置机密值，必须获取名为 **AzureRunAsConnection** 的连接的客户端 ID。 此连接是建立 Azure 自动化实例时创建的。 若要查找此 ID，请在 Azure 自动化实例中选择“资产”。 在此处选择“连接”，然后选择“AzureRunAsConnection”服务主体。 记下“ApplicationId”值。

![Azure 自動化用戶端識別碼](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在“资产”中选择“模块”。 选择“库”，然后搜索并导入以下每个模块的更新版本：

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 在撰寫本文的當時，就下列指令碼而言，需要更新的只有先前所述的模組。 如果自动化作业失败，请确认已导入所有必要的模块及其依赖项。

检索 Azure 自动化连接的应用程序 ID 之后，必须让 Key Vault 知道此应用程序有权更新保管库中的机密。 使用以下 PowerShell 命令：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下来，选择 Azure 自动化实例下的“Runbook”，然后选择“添加 Runbook”。 選取 [快速建立] 。 为 Runbook 命名，然后选择“PowerShell”作为 Runbook 类型。 可以添加说明。 最後，選取 [建立]。

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

$secret = Set-AzureRmKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

在编辑器窗格中，选择“测试”窗格以测试脚本。 正常运行脚本后，可以选择“发布”，并在 Runbook 配置窗格中应用 Runbook 的计划。

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
3. 選取 **建立資源** > **企業整合** > **服務匯流排**，然後輸入必要的詳細資料。
4. 尋找選取命名空間，然後選取服務匯流排連接資訊**連接資訊**。 您將需要此資訊在下一節。

下一步[建立 Azure 函式](../azure-functions/functions-create-first-azure-function.md)以輪詢儲存體帳戶內的金鑰保存庫記錄檔，並挑選新的事件。 依排程，就會觸發此函式。

若要建立 Azure 函數應用程式，請選取**建立資源**，在 marketplace 內搜尋**函式應用程式**，然後選取**建立**。 在建立期間，您可以使用現有的主控方案，或建立新的方案。 您也可以選擇動態主控。 如需 Azure Functions 的裝載選項的詳細資訊，請參閱[如何調整 Azure Functions](../azure-functions/functions-scale.md)。

建立 Azure 函數應用程式之後，請移至它，然後選取**計時器**案例並**C\#** 語言。 然後選取**建立此函式**。

![Azure Functions 啟動刀鋒視窗](./media/keyvault-keyrotation/Azure_Functions_Start.png)

在 [開發] 索引標籤上，以下列內容取代 run.csx 程式碼︰

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

新增類型的輸出**Azure Blob 儲存體**。 此輸出會指向您在輸入中定義的 sync.txt 檔案。 函式會使用此輸出寫入查看的最後一個事件的時間戳記。 前面的程式碼預期此參數名稱為 outputBlob。

此函式已準備好。 請務必切換回 [開發] 索引標籤並儲存程式碼。 檢查有任何編譯錯誤 [輸出] 視窗，並視需要加以更正。 如果程式碼會編譯，然後程式碼應該現在會每隔一分鐘檢查金鑰保存庫記錄檔並將任何新事件推送至定義的服務匯流排佇列。 您應該會在每次觸發函式時看到記錄資訊寫入到 [記錄檔] 視窗。

### <a name="azure-logic-app"></a>Azure 邏輯應用程式

接下來，您必須建立 Azure 邏輯應用程式，以挑選函式推送至服務匯流排佇列、 剖析內容，並傳送電子郵件符合的條件為基礎的事件。

[建立 logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)藉由選取**建立資源** > **整合** > **邏輯應用程式**。

建立邏輯應用程式之後，請移至它，然後選取**編輯**。 在邏輯應用程式編輯器中，選取**服務匯流排佇列**並輸入您的服務匯流排認證以連接至佇列。

![Azure 邏輯應用程式服務匯流排](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

選取 [新增條件] 。 在條件中，切換到進階編輯器，並輸入下列程式碼。 取代*APP_ID*實際的應用程式識別碼，為您的 web 應用程式：

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

這個運算式基本上會傳回**假**如果*appid*從內送事件 （這是服務匯流排訊息的主體） 不*appid*應用程式。

現在，底下建立動作**如果否，不要執行任何**。

![Azure 邏輯應用程式選擇動作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

動作中，選取**Office 365-傳送電子郵件**。 填寫欄位，以建立定義的條件傳回 **false** 時要傳送的電子郵件。 如果您沒有 Office 365，尋找替代項目來達到相同的結果。

您現在有新的金鑰保存庫稽核記錄檔尋找一分鐘的端對端管線。 它會將它找到新的記錄檔推送至服務匯流排佇列中。 新訊息放入佇列時就會觸發邏輯應用程式。 如果*appid*內事件的呼叫端的應用程式的應用程式識別碼不符，則會傳送一封電子郵件。
