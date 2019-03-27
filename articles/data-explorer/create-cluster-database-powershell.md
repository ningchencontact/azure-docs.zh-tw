---
title: 快速入門：使用 PowerShell 建立 Azure 資料總管叢集與資料庫
description: 了解如何使用 PowerShell 建立 Azure 資料總管叢集與資料庫
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287361"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>使用 PowerShell 建立 Azure 資料總管叢集與資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


本快速入門說明如何使用 PowerShell 建立 Azure 資料總管叢集和資料庫。

您可以在 Windows、Linux 或 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 中執行 PowerShell Cmdlet 和指令碼，以建立及設定 [Azure 資料總管](https://docs.microsoft.com/azure/kusto/ )。

[**Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )。 使用 Azure PowerShell 和 **Az.Kusto**，您可以執行下列工作：

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="configure-parameters"></a>設定參數

如果您在 Azure Cloud Shell 中執行命令，則不需要執行下列步驟。 如果您在本機執行 CLI，請依照下列步驟登入 Azure 並設定您目前的訂用帳戶：

1. 執行下列命令以登入 Azure：

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 設定要建立叢集的訂用帳戶。

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. 在您的裝置上安裝 Az.Kusto 模組：
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>建立 Azure 資料總管叢集

1. 使用下列命令建立您的叢集：

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | Name | *mykustocluster* | 所需的叢集名稱。|
   | SKU | *D13_v2* | 將用於叢集的 SKU。 |
   | resourceGroupName | *testrg* | 將在其中建立叢集的資源群組名稱。 |

    有其他選擇性參數可供您使用，例如叢集的容量。

2. 執行下列命令來檢查是否已成功建立叢集：

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

如果結果中包含有 `Succeeded` 值的 `provisioningState`，表示已成功建立叢集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集中建立資料庫

1. 使用下列命令建立您的資料庫：

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | ClusterName | *mykustocluster* | 將在其中建立資料庫的叢集名稱。|
   | Name | *mykustodatabase* | 您的資料庫名稱。|
   | resourceGroupName | *testrg* | 將在其中建立叢集的資源群組名稱。 |
   | SoftDeletePeriod | *3650:00:00:00* | 將保留資料以供查詢的時間長度。 |
   | HotCachePeriod | *3650:00:00:00* | 資料將保留在快取中的時間長度。 |

2. 執行下列命令以查看您所建立的資料庫：

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

您此時有一個叢集和一個資料庫。

## <a name="clean-up-resources"></a>清除資源

* 如果您打算按照其他快速入門和教學課程繼續進行，請保留您建立的資源。
* 若要清除資源，請刪除叢集。 您刪除叢集時，也會刪除其中的所有資料庫。 使用下列命令刪除您的叢集：

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>後續步驟

您可以在[**這裡**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )找到更多 Az.Kusto 命令

> [!div class="nextstepaction"]
> [快速入門：使用 Azure 資料總管 .NET Standard SDK 內嵌資料 (預覽)](net-standard-ingest-data.md)
