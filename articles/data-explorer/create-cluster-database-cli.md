---
title: 使用 CLI 建立 Azure 資料總管叢集與資料庫
description: 這篇文章說明如何使用 Azure CLI 建立 Azure 資料總管叢集和資料庫
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812679"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>使用 CLI 建立 Azure 資料總管叢集與資料庫

這篇文章說明如何使用 Azure CLI 建立 Azure 資料總管叢集和資料庫。

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>管理平面和資料平面 API 之間的差異為何

有兩個不同的 API 程式庫，亦即管理和資料平面 API。
管理 API 是用於管理資源、例如建立叢集、建立資料庫、刪除資料連線、變更執行個體計數等等。資料平面 API 是用於與資料互動、執行查詢、內嵌資料等等。

## <a name="configure-the-cli-parameters"></a>設定 CLI 參數

登入您的帳戶

```Bash
az login
```

設定您想要建立叢集的訂用帳戶。

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>建立 Azure 資料總管叢集

使用下列命令建立您的叢集。

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

提供下列值

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | name | *azureclitest* | 所需的叢集名稱。|
   | sku | *D13_v2* | 將用於叢集的 SKU。 |
   | resource-group | *testrg* | 將在其中建立叢集的資源群組名稱。 |

如果您想要，有更多選擇性參數可供您使用，例如叢集的容量等等。

若要檢查是否已成功建立叢集，您可以執行

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

如果結果包含有 "Succeeded" 值的 "provisioningState"，表示已成功建立叢集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集中建立資料庫

使用下列命令建立您的資料庫。

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

提供下列值

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | 叢集名稱 | *azureclitest* | 應該建立的叢集名稱。|
   | name | *clidatabase* | 所需的資料庫名稱。|
   | resource-group | *testrg* | 將在其中建立叢集的資源群組名稱。 |
   | soft-delete-period | *3650:00:00:00* | 應該保留資料以供查詢的時間長度。 |
   | hot-cache-period | *3650:00:00:00* | 應該在快取中保留資料的時間長度。 |

您可以看到您建立的資料庫，方法是執行

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

您此時會有一個叢集和一個資料庫。

## <a name="clean-up-resources"></a>清除資源

如果您打算按照其他快速入門和教學課程繼續進行，請保留您建立的資源。

您刪除叢集時，也會刪除其中的所有資料庫。 使用下列命令來刪除您的叢集。

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：將資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md)
