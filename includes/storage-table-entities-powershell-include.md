---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159750"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>管理資料表實體

現在，您有一個資料表，讓我們看看如何管理資料表中的實體 (或稱資料列)。 

實體可以有最多 255 個屬性，包括三個系統屬性：**PartitionKey**， **RowKey**，以及**時間戳記**。 您負責插入及更新的值**PartitionKey**並**RowKey**。 伺服器會管理的值**時間戳記**，無法修改。 **PartitionKey** 和 **RowKey** 的組合可唯一識別資料表內的每個實體。

* **PartitionKey**：決定儲存實體的資料分割。
* **RowKey**：可唯一識別資料分割內的實體。

您可以為每個實體最多定義 252 個自訂屬性。 

### <a name="add-table-entities"></a>新增資料表實體

將實體新增至資料表，使用**新增 AzTableRow**。 這些範例會使用資料分割索引鍵具有值`partition1`和`partition2`，和資料列索引鍵等於州名縮寫。 在每個實體的屬性是`username`和`userid`。 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>查詢資料表實體

您可以使用來查詢資料表中的實體**Get AzTableRow**命令。

> [!NOTE]
> Cmdlet **Get-azurestoragetablerowall**， **Get-azurestoragetablerowbypartitionkey**， **Get-azurestoragetablerowbycolumnname**，和**Get-azurestoragetablerowbycustomfilter**已被取代，並且將在未來版本的更新中移除。

#### <a name="retrieve-all-entities"></a>擷取所有實體

```powershell
Get-AzTableRow -table $cloudTable | ft
```

此命令會產生類似下列資料表的結果：

| userid | username | 分割區 | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>擷取特定分割區的實體

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

結果會類似下列資料表：

| userid | username | 分割區 | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>擷取特定資料行中特定值的實體

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

此查詢會擷取一筆記錄。

|field|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>使用自訂篩選條件擷取實體 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

此查詢會擷取一筆記錄。

|field|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>更新實體 

更新實體有三個步驟。 首先，取出要變更的實體。 第二，進行變更。 第三，使用變更來認可**更新 AzTableRow**。

更新使用者名稱 = 'Jessie' 的實體，讓使用者名稱 = 'Jessie2'。 此範例也顯示了另一種使用 .NET 類型建立自訂篩選條件的方法。

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

結果會顯示 Jessie2 記錄。

|field|value|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>刪除資料表實體

您可以刪除資料表中的一個實體或所有實體。

#### <a name="deleting-one-entity"></a>刪除單一實體

若要刪除單一實體，取得該實體的參考，並使用管線傳送到**移除 AzTableRow**。

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>刪除資料表中的所有實體

若要刪除資料表中的所有實體，請將它們取出，並使用管線將它們傳遞到 remove Cmdlet。 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
