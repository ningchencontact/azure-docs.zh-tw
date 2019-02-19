---
title: 快速入門：使用 CLI 建立 Azure 資料總管叢集與資料庫
description: 在本快速入門中，您將了解如何使用 Azure CLI，建立 Azure 資料總管叢集與資料庫。
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 9e0ae547df34594674dc03702310a1537717a4ed
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881111"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-cli"></a>使用 CLI 建立 Azure 資料總管叢集與資料庫

本快速入門說明如何使用 Azure CLI 建立 Azure 資料總管叢集和資料庫。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 Azure CLI，本快速入門需要有 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 來檢查您的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="configure-the-cli-parameters"></a>設定 CLI 參數

如果您在 Cloud Shell 中執行命令，則不需要執行下列步驟。 如果您在本機執行 CLI，請執行下列步驟來登入 Azure 並設定您目前的訂用帳戶：

1. 執行下列命令以登入 Azure：

    ```azurecli-interactive
    az login
    ```

2. 設定您想要建立叢集的訂用帳戶。 以您要使用的 Azure 訂用帳戶名稱取代 `MyAzureSub`：

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>建立 Azure 資料總管叢集

1. 使用下列命令建立您的叢集：

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | name | *azureclitest* | 所需的叢集名稱。|
   | sku | *D13_v2* | 將用於叢集的 SKU。 |
   | resource-group | *testrg* | 將在其中建立叢集的資源群組名稱。 |

    有其他選擇性參數可供您使用，例如叢集的容量。

2. 執行下列命令來檢查是否已成功建立叢集：

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

如果結果包含有 "Succeeded" 值的 "provisioningState"，則會成功建立叢集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集中建立資料庫

1. 使用下列命令建立您的資料庫：

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | 叢集名稱 | *azureclitest* | 應在其中建立資料庫的叢集名稱。|
   | name | *clidatabase* | 所需的資料庫名稱。|
   | resource-group | *testrg* | 將在其中建立叢集的資源群組名稱。 |
   | soft-delete-period | *3650:00:00:00* | 應該保留資料以供查詢的時間長度。 |
   | hot-cache-period | *3650:00:00:00* | 應該在快取中保留資料的時間長度。 |

2. 執行下列命令來查看您所建立的資料庫：

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

您此時有一個叢集和一個資料庫。

## <a name="clean-up-resources"></a>清除資源

* 如果您打算按照其他快速入門和教學課程繼續進行，請保留您建立的資源。
* 若要清除資源，請刪除叢集。 您刪除叢集時，也會刪除其中的所有資料庫。 使用下列命令來刪除您的叢集：

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：使用 Azure 資料總管 Python 程式庫內嵌資料](python-ingest-data.md)
