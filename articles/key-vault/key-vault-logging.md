---
title: Azure 金鑰保存庫記錄 |Microsoft Docs
description: 使用本教學課程來協助您開始使用 Azure 金鑰保存庫記錄。
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 0ae977215e52883e190ad3859eefac9e97462968
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205953"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 記錄

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

建立一或多個金鑰保存庫之後，您可能會想要監視金鑰保存庫如何及何時會存取，以及由誰。 您可以啟用 Azure Key Vault，將資訊儲存在您所提供的 Azure 儲存體帳戶的記錄功能來執行這項操作。 新的容器，名為**insights-記錄-auditevent**會自動為您指定的儲存體帳戶建立。 您可以使用這個相同的儲存體帳戶收集記錄檔，針對多個金鑰保存庫。

您可以存取您的記錄資訊 10 分鐘的時間 （最多） 金鑰保存庫作業之後。 但大多不用這麼久。  儲存體帳戶中的記錄由您全權管理：

* 請使用標準的 Azure 存取控制方法限制可存取記錄的人員，藉此來保護記錄。
* 刪除不想繼續保留在儲存體帳戶中的記錄。

使用本教學課程來協助您開始使用 Azure 金鑰保存庫記錄。 您會建立儲存體帳戶、 啟用記錄功能，並解譯所收集的記錄資訊。  

> [!NOTE]
> 本教學課程不會指示如何建立金鑰保存庫、金鑰或密碼。 如需此資訊，請參閱[什麼是 Azure Key Vault？](key-vault-overview.md)。 或者，您也可以跨平台 Azure CLI 的指示，請參閱[此對等的教學課程](key-vault-manage-with-cli2.md)。
>
> 本文章提供 Azure PowerShell 來更新診斷記錄的指示。 您也可以使用 Azure 監視器中，更新診斷記錄**診斷記錄**Azure 入口網站區段。 
>

如需 Key Vault 的概觀資訊，請參閱 <<c0> [ 什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)。 如需可使用金鑰保存庫的資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您必須具備下列項目：

* 所使用的現有金鑰保存庫。  
* Azure PowerShell 時，最低版本為 1.0.0。 若要安裝 Azure PowerShell，並將它與 Azure 訂用帳戶建立關聯，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。 如果您已安裝 Azure PowerShell，並不知道版本，從 Azure PowerShell 主控台中，輸入`$PSVersionTable.PSVersion`。  
* 足夠的 Azure 儲存體以儲存金鑰保存庫記錄。

## <a id="connect"></a>連接到您的金鑰保存庫訂用帳戶

設定索引鍵的記錄的第一個步驟是您想要記錄的金鑰保存庫的點 Azure PowerShell。

使用下列命令開始 Azure PowerShell 工作階段，並登入您的 Azure 帳戶：  

```powershell
Connect-AzAccount
```

在快顯瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱與密碼。 Azure PowerShell 會取得與此帳戶相關聯的所有訂閱。 根據預設，PowerShell 會使用第一個。

您可能必須指定您用來建立金鑰保存庫訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzSubscription
```

然後，若要指定將會記錄您的金鑰保存庫相關聯的訂用帳戶，請輸入：

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

指向正確的訂用帳戶中的 PowerShell 是一個重要步驟是，，特別是如果您有多個與您帳戶相關聯的訂用帳戶。 如需設定 Azure PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

## <a id="storage"></a>建立您的記錄檔的儲存體帳戶

雖然您可以使用現有的儲存體帳戶來儲存記錄，我們將建立專用於金鑰保存庫記錄儲存體帳戶。 為了方便起見，當我們需要指定此更新版本，我們將詳細資料儲存在名為的變數**sa**。

為了進一步簡化管理，我們也將包含金鑰保存庫在其中使用相同的資源群組。 從[快速入門教學課程](key-vault-get-started.md)，此資源群組名稱為**ContosoResourceGroup**，並將繼續使用 「 東亞 」 位置。 使用您自己的情況，就適用的情況下取代這些值：

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> 如果您決定使用現有的儲存體帳戶，它必須使用相同的訂用帳戶和金鑰保存庫。 而且它必須使用 Azure Resource Manager 部署模型中，而不是傳統部署模型。
>
>

## <a id="identify"></a>識別用來儲存記錄的金鑰保存庫

在 [快速入門教學課程](key-vault-get-started.md)，金鑰保存庫名稱已**ContosoKeyVault**。 我們會繼續使用該名稱，並將詳細資料儲存在名為的變數**kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>啟用記錄

若要啟用金鑰保存庫記錄，我們將使用**組 AzDiagnosticSetting** cmdlet，並搭配我們新的儲存體帳戶和金鑰保存庫建立的變數。 我們也會設定 **-啟用**旗標設為 **$true**並將類別目錄設定為**AuditEvent** （金鑰保存庫記錄的唯一類別）：

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

輸出如下所示：

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

此輸出會確認記錄現在已啟用金鑰保存庫，它會將資訊儲存至儲存體帳戶。

（選擇性） 您可以設定保留原則來儲存記錄，會自動刪除較舊的記錄檔。 例如，設定保留原則設定 **-RetentionEnabled**旗標設為 **$true**，並將 **-RetentionInDays**參數來**90**以便自動刪除超過 90 天的記錄檔。

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

所記錄的內容：

* 所有經過驗證的 REST API 要求，包括因為存取權限、 系統錯誤或要求錯誤而失敗的要求。
* 金鑰的作業保存庫本身，包括建立、 刪除、 設定金鑰保存庫存取原則，以及更新金鑰保存庫屬性，例如標記。
* 金鑰保存庫中金鑰和密碼的作業包括：
  * 建立、 修改或刪除這些金鑰或祕密。
  * 簽署、 驗證、 加密、 解密、 包裝和解除包裝金鑰，讓祕密及清單的金鑰和祕密 （和其版本）。
* 產生 401 回應的未經驗證要求。 範例包括沒有的要求的持有人權杖，格式不正確或已過期，或具有無效的語彙基元。  

## <a id="access"></a>存取記錄

金鑰保存庫記錄會儲存在**insights-記錄-auditevent**您提供儲存體帳戶中的容器。 若要檢視記錄檔，您必須下載 blob。

首先，建立容器名稱的變數。 您將使用此逐步解說的其餘部分的變數。

```powershell
$container = 'insights-logs-auditevent'
```

若要列出此容器中的所有 blob，請輸入：

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

輸出大致如下：

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

在此輸出中我們可以看到，blob 遵循以下命名慣例：`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日期和時間值使用 UTC。

因為您可以使用相同的儲存體帳戶來收集多個資源的記錄，blob 名稱中的完整資源識別碼是適合用來存取，或只是您需要將 blob 下載。 但在這麼做之前，我們要先討論如何下載所有 blob。

建立資料夾來下載 blob。 例如:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

然後取得所有 blob 的清單：  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

使用這份清單透過管線傳送**Get AzStorageBlobContent**下載 blob 到目的地資料夾：

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

在執行第二個命令時，blob 名稱中的 **/** 分隔符號會在目的地資料夾下建立完整資料夾結構。 若要下載 blob 並儲存為檔案，您將使用此結構。

若要有所選擇地下載 blob，請使用萬用字元。 例如:

* 如果您有多個金鑰保存庫，並且只想下載其中的 CONTOSOKEYVAULT3 金鑰保存庫的記錄：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 如果您有多個資源群組，並且只想下載其中某個資源群組的記錄，請使用 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 如果您要下載 2019 年 1 月的月的所有記錄檔，請使用`-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

您現在已做好準備，可以開始查看記錄中有何內容。 但是之前我們繼續進行，您應該知道更多的兩個命令,：

* 若要查詢金鑰保存庫資源的診斷設定狀態：`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* 若要停用金鑰保存庫資源記錄： `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>解譯金鑰保存庫記錄

各個 blob 皆會儲存為文字，並格式化為 JSON blob。 讓我們看看一個範例記錄項目。 請執行這個命令：

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

它會傳回類似如下的記錄項目：

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

下表列出的欄位名稱和描述：

| 欄位名稱 | 描述 |
| --- | --- |
| **time** |日期和時間-utc 時區。 |
| **resourceId** |Azure Resource Manager 資源識別碼。 對於金鑰保存庫記錄來說，這一律是金鑰保存庫的資源識別碼。 |
| **operationName** |下一份表格所述作業的名稱。 |
| **operationVersion** |用戶端要求的 REST API 版本。 |
| **類別** |結果類型。 對於金鑰保存庫記錄來說， **AuditEvent**是可用的值。 |
| **resultType** |REST API 要求的結果。 |
| **resultSignature** |HTTP 狀態。 |
| **resultDescription** |結果的其他描述 (若有提供)。 |
| **durationMs** |服務 REST API 要求時所花費的時間，以毫秒為單位。 此時間不包括網路延遲，因此在用戶端所測量到的時間可能不符合此時間。 |
| **callerIpAddress** |提出要求的用戶端的 IP 位址。 |
| **correlationId** |選擇性的 GUID，用戶端可傳遞此 GUID 來讓用戶端記錄與服務端 (金鑰保存庫) 記錄相互關聯。 |
| **身分識別** |從所提供的 REST API 要求的權杖的身分識別。 這通常是 「 使用者 」，「 服務主體 」 或在要求所產生的 Azure PowerShell cmdlet 的情況下 「 使用者 + appId、 」 組合。 |
| **properties** |不同的資訊為基礎的作業 (**operationName**)。 在大部分情況下，此欄位會包含用戶端資訊 （用戶端所傳遞的使用者代理字串）、 確切的 REST API 要求 URI 和 HTTP 狀態碼。 此外，當您傳回要求的結果物件時，才 (例如**KeyCreate**或**VaultGet**)，它也包含金鑰 URI （做為 「 識別碼 」），保存庫 URI 或密碼的 URI。 |

**OperationName**欄位值位於*ObjectVerb*格式。 例如:

* 所有金鑰保存庫作業都有`Vault<action>`格式，例如`VaultGet`和`VaultCreate`。
* 所有金鑰作業都有`Key<action>`格式，例如`KeySign`和`KeyList`。
* 所有密碼作業都有`Secret<action>`格式，例如`SecretGet`和`SecretListVersions`。

下表列出**operationName**值和相對應的 REST API 命令：

| operationName | REST API 命令 |
| --- | --- |
| **驗證** |透過 Azure Active Directory 端點驗證 |
| **VaultGet** |[取得金鑰保存庫的相關資訊](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[建立或更新金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[刪除金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[更新金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[列出資源群組中的所有金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[建立金鑰](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[取得金鑰的相關資訊](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[將金鑰匯入保存庫](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[備份金鑰](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[刪除金鑰](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[還原金鑰](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[使用金鑰簽署](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[使用金鑰驗證](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[包裝金鑰](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[解除包裝金鑰](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[使用金鑰加密](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[使用金鑰解密](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[更新金鑰](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[列出保存庫中的金鑰](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[列出金鑰的版本](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[建立密碼](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[取得祕密](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[更新密碼](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[刪除秘密](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[列出保存庫中的密碼](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[列出密碼的版本](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>您可以使用 Azure 監視器記錄

您可以在 Azure 監視器記錄檔中使用的 Key Vault 解決方案來檢閱金鑰保存庫**AuditEvent**記錄檔。 在 Azure 監視器記錄中，您可以使用記錄檔查詢來分析資料，並取得您所需的資訊。 

如需詳細資訊，包括如何設定此功能，請參閱 < [Azure 監視器記錄檔中的 Azure Key Vault 解決方案](../azure-monitor/insights/azure-key-vault.md)。 這篇文章也包含指示，如果您需要從 Azure 監視器記錄檔期間提供的預覽，請先傳送您的 Azure 儲存體帳戶的記錄，並設定的 Azure 監視器記錄至從中讀取的舊 Key Vault 解決方案移轉。

## <a id="next"></a>接續步驟

使用 Azure Key Vault 中的.NET web 應用程式的教學課程，請參閱[從 web 應用程式使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。

如需程式設計參考，請參閱 [Azure 金鑰保存庫開發人員指南](key-vault-developers-guide.md)。

如需 Azure 金鑰保存庫的 Azure PowerShell 1.0 cmdlet 的清單，請參閱 < [Azure 金鑰保存庫 cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)。

如需金鑰輪替和稽核 Azure Key vault 的記錄檔的教學課程，請參閱 <<c0> [ 使用端對端金鑰輪替和稽核設定金鑰保存庫](key-vault-key-rotation-log-monitoring.md)。
