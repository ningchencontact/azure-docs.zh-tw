---
title: 使用 C# 建立 Azure 資料總管叢集與資料庫
description: 了解如何使用 C# 建立 Azure 資料總管叢集與資料庫
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e51551d4ce8061122fce52b05e68e102b71c27a8
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494615"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>使用 C# 建立 Azure 資料總管叢集與資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 在本文中，您建立叢集和資料庫使用C#。

## <a name="prerequisites"></a>必要條件

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="install-c-nuget"></a>安裝 C# Nuget

1. 安裝 [Azure 資料總管 (Kusto) NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。

1. 安裝用來驗證的 [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet 套件](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)。

## <a name="create-the-azure-data-explorer-cluster"></a>建立 Azure 資料總管叢集

1. 使用下列程式碼建立您的叢集：

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | clusterName | *mykustocluster* | 所需的叢集名稱。|
   | sku | *D13_v2* | 將用於叢集的 SKU。 |
   | resourceGroupName | *testrg* | 將在其中建立叢集的資源群組名稱。 |

    有其他選擇性參數可供您使用，例如叢集的容量。

1. 設定[您的認證](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)

1. 執行下列命令來檢查是否已成功建立叢集：

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

如果結果中包含有 `Succeeded` 值的 `ProvisioningState`，表示已成功建立叢集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集中建立資料庫

1. 使用下列程式碼建立您的資料庫：

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | clusterName | *mykustocluster* | 將在其中建立資料庫的叢集名稱。|
   | databaseName | *mykustodatabase* | 您的資料庫名稱。|
   | resourceGroupName | *testrg* | 將在其中建立叢集的資源群組名稱。 |
   | softDeletePeriod | *3650:00:00:00* | 將保留資料以供查詢的時間長度。 |
   | hotCachePeriod | *3650:00:00:00* | 資料將保留在快取中的時間長度。 |

2. 執行下列命令以查看您所建立的資料庫：

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

您此時有一個叢集和一個資料庫。

## <a name="clean-up-resources"></a>清除資源

* 如果您打算遵循我們的其他文章，讓您建立的資源。
* 若要清除資源，請刪除叢集。 您刪除叢集時，也會刪除其中的所有資料庫。 使用下列命令刪除您的叢集：

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料總管 .NET Standard SDK 內嵌資料 (預覽)](net-standard-ingest-data.md)
