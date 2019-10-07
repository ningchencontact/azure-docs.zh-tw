---
title: 使用 Azure 資料總管C# SDK 建立原則
description: '在本文中，您將瞭解如何使用 c # 來建立原則。'
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: fa2dd4993dbf70bcbc6ea97726f8cd7254123429
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2019
ms.locfileid: "71997224"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-using-c"></a>使用建立適用于 Azure 資料總管的資料庫和資料表原則C#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 在本文中，您會使用C#來建立適用于 Azure 資料總管的資料庫和資料表原則。

## <a name="prerequisites"></a>必要條件

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

* [測試叢集和資料庫](create-cluster-database-csharp.md)

* [測試資料表](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

## <a name="install-c-nuget"></a>安裝C# Nuget

* 安裝 [Azure 資料總管 (Kusto) NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。

* 安裝[Kusto NETStandard nuget 封裝](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)（選擇性，用於變更資料表的原則）。

* 安裝用來驗證的 [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。

## <a name="authentication"></a>驗證
若要執行本文中的範例，我們需要 Azure AD 應用程式和服務主體，才能存取資源。 您可以使用相同的 Azure AD 應用程式，從[測試叢集和資料庫](create-cluster-database-csharp.md#authentication)進行驗證。 如果您想要使用不同的 Azure AD 應用程式，請參閱[建立 Azure AD 應用](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)程式來建立免費的 Azure AD 應用程式，並在訂用帳戶範圍新增角色指派。 它也會說明如何取得 `Directory (tenant) ID`、`Application ID` 和 @no__t 2。 您可能需要將新的 Azure AD 應用程式新增為資料庫中的主體，請參閱[管理 Azure 資料總管資料庫許可權](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)。   

## <a name="alter-database-retention-policy"></a>改變資料庫保留原則
使用10天的虛刪除週期來設定保留原則。
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Alter database 快取原則
設定資料庫的快取原則，其中最後五天的資料會在叢集 SSD 上。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Alter table cache 原則
設定資料表的快取原則，其中最後五天的資料會在叢集 SSD 上。

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-database"></a>為資料庫加入新的主體
將新的 Azure AD 應用程式新增為資料庫的系統管理主體

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>後續步驟

* [深入瞭解資料庫和資料表原則](https://docs.microsoft.com/azure/kusto/management/policies)
