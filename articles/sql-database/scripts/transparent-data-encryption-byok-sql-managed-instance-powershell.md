---
title: PowerShell：啟用 BYOK TDE-Azure SQL Database 受控執行個體 |Microsoft Docs
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
ms.date: 11/04/2019
ms.openlocfilehash: 0413216bc666aff504193d6723d46a6ee26be8ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500065"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>從 Azure Key Vault 使用自己的金鑰管理受控執行個體中的透明資料加密

此 PowerShell 腳本範例會使用 Azure Key Vault 中的金鑰，在 Azure SQL 受控執行個體的攜帶您自己的金鑰（預覽）案例中設定透明資料加密（TDE）。 若要深入了解具有「攜帶您自己的金鑰」(BYOK) 支援的 TDE，請參閱[將 TDE 攜帶您自己的金鑰用於 Azure SQL](../transparent-data-encryption-byok-azure-sql.md)。

## <a name="prerequisites"></a>必要條件

- 現有的受控執行個體。 請參閱[使用 PowerShell 建立 Azure SQL Database 受控實例](sql-database-create-configure-managed-instance-powershell.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本機使用 PowerShell 或使用 Azure Cloud Shell 需要 AZ PowerShell 1.1.1-preview 或較新的預覽版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)，或執行下列範例腳本來安裝模組。

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="sample-scripts"></a>範例指令碼

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

其他的 SQL Database PowerShell 指令碼範例可於 [Azure SQL Database PowerShell 指令碼](../sql-database-powershell-samples.md)中找到。
