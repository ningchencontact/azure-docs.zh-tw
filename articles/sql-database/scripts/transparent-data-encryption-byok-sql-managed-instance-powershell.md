---
title: PowerShell：啟用 BYOK TDE - Azure SQL Database 受控執行個體 | Microsoft Docs
description: 了解如何設定 Azure SQL 受控執行個體，以透過 PowerShell 開始使用 BYOK 透明資料加密 (TDE) 進行待用資料加密。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: c2c4bd7bffd923430d0817cb6ea975f4c1596623
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729170"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>管理中使用您自己的金鑰，從 Azure Key Vault （預覽） 的受控執行個體的透明資料加密

此 PowerShell 指令碼範例會設定 Azure SQL 受控執行個體，使用 Azure Key vault 金鑰在攜帶您自己的金鑰 （預覽） 案例中的透明資料加密 (TDE)。 若要深入了解具有「攜帶您自己的金鑰」(BYOK) 支援的 TDE，請參閱[將 TDE 攜帶您自己的金鑰用於 Azure SQL](../transparent-data-encryption-byok-azure-sql.md)。

## <a name="prerequisites"></a>必要條件

- 現有的 「 受控執行個體 」。 請參閱[使用 PowerShell 建立 Azure SQL Database 受控執行個體](sql-database-create-configure-managed-instance-powershell.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本機使用這兩個 PowerShell，或使用 Azure Cloud Shell 需要 AZ PowerShell 1.1.1-preview 或更新版本的預覽版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)，或執行下列安裝模組的範例指令碼。

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="sample-scripts"></a>範例指令碼

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。
