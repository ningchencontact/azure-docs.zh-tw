---
title: 使用資料的資料庫功能，在 Azure 中附加資料庫資料總管
description: 瞭解如何使用「資料後資料庫」功能，在 Azure 資料總管中附加資料庫。
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 2306b6cbdd347e3be9921b196ae06385ef5ca90a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083198"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>使用在 Azure 資料總管中的資料，來連接資料庫

「後置**資料庫**」功能可讓您將位於不同叢集中的資料庫附加至您的 Azure 資料總管叢集。 「執行程式」**資料庫**是以*唯讀*模式附加，因此您可以在內嵌至**領導者資料庫**的資料上，查看資料並執行查詢。 「資料後」資料庫會同步處理領導者資料庫中的變更。 由於同步處理，資料可用性會有幾秒鐘到幾分鐘的資料延遲。 時間延遲的長度取決於領導者資料庫中繼資料的整體大小。 領導者和執行者資料庫會使用相同的儲存體帳戶來提取資料。 儲存區是由領導者資料庫所擁有。 「取用的資料庫」會在不需要內嵌資料的情況下，就能流覽資料。 因為附加的資料庫是唯讀資料庫，所以無法修改資料庫中的資料、資料表和原則，但快取[原則](#configure-caching-policy)、[主體](#manage-principals)和[許可權](#manage-permissions)除外。 無法刪除附加的資料庫。 它們必須由領導者或其他人卸離，然後才可以刪除。 

使用「進行中」功能將資料庫附加至不同的叢集，是用來做為基礎結構，以便在組織與小組之間共用資料。 此功能適用于隔離計算資源，以保護生產環境不受非生產使用案例的限制。 您也可以將 Azure 資料總管叢集的成本與對資料執行查詢的合作物件建立關聯。

## <a name="which-databases-are-followed"></a>會遵循哪些資料庫？

* 叢集可以遵循一個資料庫、數個資料庫或領導者叢集的所有資料庫。 
* 單一叢集可以遵循來自多個領導者叢集的資料庫。 
* 叢集可以同時包含進行中的資料庫和領導者資料庫

## <a name="prerequisites"></a>先決條件

1. 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。
1. 為領導者和進行中[建立叢集和資料庫](/azure/data-explorer/create-cluster-database-portal)。
1. 使用內嵌[總覽](/azure/data-explorer/ingest-data-overview)中所討論的各種方法之一，將[資料](/azure/data-explorer/ingest-sample-data)內嵌至領導者資料庫。

## <a name="attach-a-database"></a>連結資料庫

您可以使用各種方法來附加資料庫。 在本文中，我們會討論如何使用C#或 Azure Resource Manager 範本附加資料庫。 若要附加資料庫，您必須擁有領導者叢集和消費者叢集的許可權。 如需許可權的詳細資訊，請參閱[管理許可權](#manage-permissions)。

### <a name="attach-a-database-using-c"></a>使用附加資料庫C#

**必要的 Nuget**

* 請安裝[kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝[ClientRuntime 以進行驗證](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)。


```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db" // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本附加資料庫

在本節中，您將瞭解如何使用[Azure Resource Manager 範本](../azure-resource-manager/resource-group-overview.md)附加資料庫。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>部署範本 

您可以[使用 Azure 入口網站](https://portal.azure.com)或使用 powershell 來部署 Azure Resource Manager 範本。

   ![範本部署](media/follower/template-deployment.png)


|**設定**  |**描述**  |
|---------|---------|
|進行中的叢集名稱     |  進行中的叢集名稱       |
|附加的資料庫設定名稱    |    附加的資料庫設定物件的名稱。 名稱在叢集層級必須是唯一的。     |
|資料庫名稱     |      要遵循的資料庫名稱。 如果您想要追蹤所有領導者的資料庫，請使用 ' * '。   |
|領導者叢集資源識別碼    |   領導者叢集的資源識別碼。      |
|預設主體修改種類    |   預設的主體修改種類。 可以是 `Union`、`Replace` 或 `None`。 如需預設主體修改種類的詳細資訊，請參閱[principal 修改種類控制命令](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind)。      |
|位置   |   所有資源的位置。 領導人和進行者必須位於相同的位置。       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>確認資料庫已成功附加

若要確認資料庫是否已成功附加，請在[Azure 入口網站](https://portal.azure.com)中尋找您附加的資料庫。 

1. 流覽至 [進行中] 叢集並選取 [**資料庫**]
1. 在 [資料庫] 清單中搜尋新的唯讀資料庫。

    ![唯讀的進行中資料庫](media/follower/read-only-follower-database.png)

或者：

1. 流覽至領導者叢集並選取 [**資料庫**]
2. 檢查相關資料庫是否標示為**與其他人共用** > **是**

    ![讀取和寫入附加的資料庫](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>使用卸離後的資料庫C# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>卸離已連接的資料來源資料庫與進行中的叢集

「進行中」叢集可以卸離任何附加的資料庫，如下所示：

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>從領導者叢集卸離附加的資料來源資料庫

領導者叢集可以卸離任何附加的資料庫，如下所示：

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="manage-principals-permissions-and-caching-policy"></a>管理主體、許可權和快取原則

### <a name="manage-principals"></a>管理主體

附加資料庫時，請指定**預設的主體修改種類**。 預設會保留已[授權主體](/azure/kusto/management/access-control/index#authorization)的領導者資料庫集合

|**種類** |**描述**  |
|---------|---------|
|**並**     |   附加的資料庫主體一律會包含原始資料庫主體，加上加入至資料後資料庫的額外新主體。      |
|**Replace**   |    不會繼承原始資料庫的主體。 必須為附加的資料庫建立新的主體。 至少必須將一個主體新增至區塊主體繼承。     |
|**None**   |   附加的資料庫主體只包含原始資料庫的主體，且沒有其他主體。      |

如需使用控制命令來設定授權主體的詳細資訊，請參閱[控制管理使用](/azure/kusto/management/cluster-follower)中叢集的命令。

### <a name="manage-permissions"></a>管理許可權

管理唯讀資料庫許可權與所有資料庫類型相同。 請參閱[管理 Azure 入口網站中的許可權](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

### <a name="configure-caching-policy"></a>設定快取原則

「後向資料庫管理員」可以在主控叢集上修改附加資料庫或其任何資料表的快取[原則](/azure/kusto/management/cache-policy)。 預設值是保留資料庫和資料表層級快取原則的領導者資料庫集合。 例如，您可以在領導者資料庫上有30天的快取原則來執行每月報表，並在執行者資料庫上使用三天的快取原則，只查詢最近的資料進行疑難排解。 如需使用控制命令在資料後資料庫或資料表上設定快取原則的詳細資訊，請參閱[控制用於管理](/azure/kusto/management/cluster-follower)資料型叢集的命令。

## <a name="limitations"></a>限制

* 「進行中」和「領導者」叢集必須位於相同的區域。
* [資料流程](/azure/data-explorer/ingest-data-streaming)內嵌無法用於所遵循的資料庫。
* 卸離另一個叢集之前，您無法刪除該資料庫。
* 卸離資料庫之前，您無法刪除已附加至不同叢集的叢集。
* 您無法停止已連接到或領導者資料庫的叢集。 

## <a name="next-steps"></a>後續步驟

* 如需有關「進行中」叢集設定的詳細資訊，請參閱[控制管理使用](/azure/kusto/management/cluster-follower)中叢集的命令。