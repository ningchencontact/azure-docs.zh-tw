---
title: Azure SQL Database 受控執行個體設定現有的 VNET/子網路 | Microsoft Docs
description: 本主題描述如何設定可以在其中部署 Azure SQL Database 受控執行個體的現有虛擬網路 (VNet) 和子網路。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 1718177a0902bc7049eb6986e5a1d128eeb3f233
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040953"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>針對 Azure SQL Database 受控執行個體設定現有的 VNet

Azure SQL Database 受控執行個體必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 以及受控執行個體專用的子網路內。 如果根據[受控執行個體 VNet 需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)設定，則可以使用現有的 VNet 和子網路。

如果您仍有新的子網路尚未設定、不確定子網路是否符合[需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)，或者在您進行了一些變更後，您想要檢查子網路是否仍符合[網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)的規範，您可以使用本節中所述的指令碼驗證及修改您的網路。

  > [!Note]
  > 您只能在 Resource Manager 虛擬網路中建立受控執行個體。 不支援使用傳統部署模型部署的 Azure VNet。 請確定依照[決定受控執行個體的子網路大小](sql-database-managed-instance-determine-size-vnet-subnet.md)一文中的指導方針來計算子網路大小，因為在子網路內部署資源後無法調整大小。

## <a name="validate-and-modify-an-existing-virtual-network"></a>驗證與修改現有的虛擬網路

如果您想要在現有子網路內建立受控執行個體，我們建議使用下列 PowerShell 指令碼來準備子網路：

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

子網路準備可以在 3 個簡單步驟中完成：

1. 驗證 - 針對受控執行個體網路需求，驗證所選虛擬網路和子網路。
2. 確認 - 使用者會看到需要進行的一組變更，以便為受控執行個體部署準備子網路，並且由系統要求同意。
3. 準備 - 虛擬網路和子網路已正確設定。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何建立 VNet、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。
- 針對 DNS 問題，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。
