---
title: 快速入門：在 Azure SQL 資料倉儲中暫停與繼續計算 - PowerShell | Microsoft Docs
description: 使用 PowerShell 來暫停 Azure SQL 資料倉儲中的計算以節省成本。 在您準備好使用資料倉儲時繼續計算。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fe9cd6c951f9eba73cee1bea66df88f3143859b9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58846837"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>快速入門：使用 PowerShell 暫停與繼續 Azure SQL 資料倉儲中的計算

使用 PowerShell 來暫停 Azure SQL 資料倉儲中的計算以節省成本。 在您準備好使用資料倉儲時[繼續計算](sql-data-warehouse-manage-compute-overview.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本快速入門假設您已有可暫停與繼續的 SQL 資料倉儲。 若您需要建立 SQL 資料倉儲，可以使用[建立與連線 - 入口網站](create-data-warehouse-portal.md)來建立稱為 **mySampleDataWarehouse** 的資料倉儲。

## <a name="log-in-to-azure"></a>登入 Azure

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Connect-AzAccount
```

若要查看您正在使用的訂用帳戶，請執行 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)。

```powershell
Get-AzSubscription
```

若要使用不同於預設值的訂用帳戶，請執行 [Set-AzContext](/powershell/module/az.accounts/set-azcontext)。

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>查詢資料倉儲資訊

找出您計畫暫停與繼續之資料倉儲的資料庫名稱、伺服器名稱和資源群組。

遵循下列步驟來尋找您資料倉儲的位置資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 Azure 入口網站的左側頁面中，按一下 [SQL 資料庫]。
3. 從 [SQL 資料庫] 頁面中，選取 [mySampleDataWarehouse]。 資料倉儲隨即開啟。

    ![伺服器名稱和資源群組](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. 記下資料倉儲名稱，這便是資料庫名稱。 也請記下伺服器名稱與資源群組。
6. 如果您的伺服器是 foo.database.windows.net，請只在 PowerShell Cmdlet 中使用其第一個部分作為伺服器名稱。 在上述映像中，完整伺服器名稱是 newserver 20171113.database.windows.net。 捨棄尾碼，並在 PowerShell Cmdlet 中使用 **newserver-20171113** 作為伺服器名稱。

## <a name="pause-compute"></a>暫停計算

為了節省成本，您可以隨選暫停和繼續計算資源。 例如，如果您在夜間和週末不會使用資料庫，可以在這段時間暫停，並且在白天時繼續。 資料庫暫停時，計算資源不會有費用。 不過，您仍需持續支付儲存體費用。

若要暫停資料庫，請使用 [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) Cmdlet。 下列範例會暫停在伺服器 **newserver-20171113** 上託管的資料倉儲 **mySampleDataWarehouse**。 此伺服器位於 Azure 資源群組 **myResourceGroup** 中。


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

一種變異，這個範例會將資料庫擷取至 $database 物件。 然後將物件輸送到 [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase)。 結果會儲存在物件 resultDatabase 中。 最終的命令會顯示結果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>繼續計算

若要啟動資料庫，請使用 [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) Cmdlet。 下列範例會啟動在伺服器 newserver-20171113 上託管的資料庫 mySampleDataWarehouse。 此伺服器位於 Azure 資源群組 myResourceGroup 中。

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

一種變異，這個範例會將資料庫擷取至 $database 物件。 接著將物件輸送到 [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase)，並將結果儲存在 $resultDatabase 中。 最終的命令會顯示結果。

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>檢查資料倉儲作業的狀態

若要檢查資料倉儲的狀態，請使用 [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) Cmdlet。

```
Get-AzSqlDatabaseActivity -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database02"
```

## <a name="clean-up-resources"></a>清除資源

您需對資料倉儲單位和資料倉儲上儲存的資料付費。 這些計算和儲存體資源會分開計費。

- 如果您想保留儲存體中的資料，請暫停計算。
- 如果您需要移除未來的費用，可以將資料倉儲刪除。

遵循下列步驟，視需要清除資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)，並按一下您的資料倉儲。

    ![清除資源](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 若要暫停計算，請按一下 [暫停] 按鈕。 資料倉儲暫停時，您會看到 [啟動] 按鈕。  若要繼續計算，請按一下 [啟動]。

3. 若要移除資料倉儲，而不再支付計算或儲存體的費用，請按一下 [刪除]。

4. 若要移除您所建立的 SQL 伺服器，請按一下 [mynewserver 20171113.database.windows.net]，然後按一下 [刪除]。  請謹慎使用刪除，因為刪除伺服器也會刪除所有指派給伺服器的資料庫。

5. 若要移除此資源群組，請按一下 [myResourceGroup]，然後按一下 [刪除資源群組]。


## <a name="next-steps"></a>後續步驟

您現已學會如何暫停與繼續資料倉儲的計算。 若要深入了解 Azure SQL 資料倉儲，請繼續進行載入資料的教學課程。

> [!div class="nextstepaction"]
> [將資料載入 SQL 資料倉儲](load-data-from-azure-blob-storage-using-polybase.md)
