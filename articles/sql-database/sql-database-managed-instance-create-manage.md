---
title: Azure SQL Database 受控執行個體的管理 API 參考 | Microsoft Docs
description: 深入了解建立及管理 Azure SQL Database 受控執行個體。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 26445017553435454e929d9852dfd9fc173c3b2e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460092"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Azure SQL Database 受控執行個體的管理 API 參考

您可以使用 Azure 入口網站、PowerShell、Azure CLI、REST API 和 Transact-SQL，來建立及管理 Azure SQL Database 受控執行個體。 在本文中，您可以概略了解建立和設定受控執行個體的函式和 API。

## <a name="azure-portal-create-a-managed-instance"></a>Azure 入口網站：建立受控執行個體

如需示範如何建立 Azure SQL Database 受控執行個體的快速入門，請參閱[快速入門：建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell：建立和管理受控執行個體

若要使用 Azure PowerShell 建立和管理受控執行個體，請使用下列 PowerShell 指令程式。 如果您需要安裝或升級 PowerShell，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

> [!TIP]
> 如需 PowerShell 範例指令碼，請參閱[指令碼快速入門：使用 PowerShell 程式庫建立 Azure SQL 受控執行個體](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)。

| Cmdlet | 說明 |
| --- | --- |
|[New-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|建立 Azure SQL Database 受控執行個體 |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlinstance)|傳回 Azure SQL Database 受控執行個體的資訊|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlinstance)|設定 Azure SQL Database 受控執行個體的屬性|
|[Remove-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqlinstance)|移除 Azure SQL Database 受控執行個體|
|[New-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstancedatabase)|建立 Azure SQL Database 受控執行個體資料庫|
|[Get-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlinstancedatabase)|傳回 Azure SQL Database 受控執行個體的資訊資料庫|
|[Remove-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqlinstancedatabase)|移除 Azure SQL Database 受控執行個體資料庫|
|[Restore-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqlinstancedatabase)|還原 Azure SQL Database 受控執行個體資料庫|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Azure CLI：建立和管理受控執行個體

若要使用 [Azure CLI](/cli/azure) 建立和管理受控執行個體，請使用下列 [Azure CLI SQL 受控執行個體](/cli/azure/sql/mi)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在您的瀏覽器中執行 CLI，或在 macOS、Linux 或 Windows 中[安裝](/cli/azure/install-azure-cli)。

> [!TIP]
> 如需 Azure CLI 快速入門，請參閱[使用 Azure CLI 處理 SQL 受控執行個體](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)。

| Cmdlet | 說明 |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |建立受控執行個體|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|列出可用的受控執行個體|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|取得受控執行個體的詳細資料|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|更新受控執行個體|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|移除受控執行個體|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |建立受控資料庫|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|列出可用的受控資料庫|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|還原受控資料庫|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|移除受控資料庫|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL：建立和管理執行個體資料庫

若要在建立受控執行個體後建立與管理執行個體資料庫，請使用下列 T-SQL 命令。 您可以使用 Azure 入口網站、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 發出這些命令。 [Visual Studio Code](https://code.visualstudio.com/docs)，或可以連接到 Azure SQL Database 伺服器並傳遞 TRANSACT-SQL 命令的其他任何程式。

> [!TIP]
> 如需快速入門顯示必須如何在 Microsoft Windows 上使用 SQL Server Management Studio 設定並連線到受控執行個體，請參閱[快速入門：設定 Azure VM 以連線到 Azure SQL Database 受控執行個體](sql-database-managed-instance-configure-vm.md)和[快速入門：設定從內部部署連線至 Azure SQL Database 受控執行個體的點對站連線](sql-database-managed-instance-configure-p2s.md)。
> [!IMPORTANT]
> 您無法使用 Transact-SQL 建立或刪除受控執行個體。

| 命令 | 說明 |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|建立新的受控執行個體資料庫。 您必須連線到 master 資料庫才能建立新的資料庫。|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |修改 Azure SQL Database 受控執行個體資料庫。|

## <a name="rest-api-create-and-manage-managed-instances"></a>REST API：建立和管理受控執行個體

若要建立和管理受控執行個體，請使用下列 REST API 要求。

| 命令 | 說明 |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|建立或更新受控執行個體。|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|刪除受控執行個體。|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|取得受控執行個體。|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|傳回訂用帳戶之中受控執行個體的清單。|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|傳回資源群組之中受控執行個體的清單。|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|更新受控執行個體。|

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何將 SQL Server 資料庫移轉至 Azure，請參閱[移轉至 Azure SQL Database](sql-database-cloud-migrate.md)。
- 如需支援功能的相關資訊，請參閱「[功能](sql-database-features.md)」。
