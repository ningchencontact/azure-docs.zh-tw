---
title: Azure SQL Database-Azure Hybrid Benefit
description: 使用現有的 SQL Server 授權來 SQL Database 折扣。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: 9a09f7aec4966c02ccfa2101f4359c510ae8b111
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691096"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

在 vCore 為基礎的購買模型的已布建計算層中，您可以使用[SQL Server 的 Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)，在 SQL Database 上交換您現有的授權以享有折扣優惠。 這項 Azure 權益可讓您使用內部部署 SQL Server 授權搭配軟體保證，最多省下 30% 的 Azure SQL Database。

![定價](./media/sql-database-service-tiers/pricing.png)


## <a name="choose-a-license-model"></a>選擇授權模型

使用 Azure Hybrid Benefit，您可以選擇只使用現有的 SQL database engine SQL Server 授權（基礎計算定價）來支付基礎 Azure 基礎結構的費用，也可以為基礎結構和 SQL Server 付費授權（包含授權的定價）。

您可以使用 Azure 入口網站或使用下列其中一個 Api，來選擇或變更您的授權模型：

- 若要使用 PowerShell 來設定或更新授權類型：

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- 若要使用 Azure CLI 來設定或更新授權類型：

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- 若要使用 REST API 來設定或更新授權類型：

  - [資料庫 - 建立或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [資料庫 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)



## <a name="next-steps"></a>後續步驟

- 如需在 SQL Database 部署選項之間進行選擇，請參閱[在 AZURE SQL 中選擇正確的部署選項](sql-database-paas-vs-sql-server-iaas.md)。
- 如需 SQL Database 功能的比較，請參閱[Azure SQL Database 功能](sql-database-features.md)。
