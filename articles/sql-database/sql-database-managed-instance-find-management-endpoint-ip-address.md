---
title: 探索 Azure SQL Database 受控執行個體管理端點
description: 了解如何取得 Azure SQL Database 受控執行個體管理端點公用 IP 位址，並驗證其內建防火牆保護
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 0cc3ca3a2b8130e52d8c7fc670c0ee7997e4aa47
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688039"
---
# <a name="determine-the-management-endpoint-ip-address"></a>判斷管理端點 IP 位址

Azure SQL Database 受控執行個體的虛擬叢集包含 Microsoft 用來進行管理作業的管理端點。 管理端點會受到網路層級內建防火牆和應用程式層級的相互憑證驗證所保護。 您可以判斷管理端點的 IP 位址，但您無法存取此端點。

若要判斷管理 IP 位址，請在受控實例的 FQDN 上進行 DNS 查閱： `mi-name.zone_id.database.windows.net`。 這會傳回類似 `trx.region-a.worker.vnet.database.windows.net`的 DNS 專案。 接著，您可以在此 FQDN 上執行 DNS 查閱，並移除 ". vnet"。 這會傳回管理 IP 位址。 

如果您將 \<MI FQDN\> 取代為受控實例的 DNS 專案，此 PowerShell 將會為您完成這項操作： `mi-name.zone_id.database.windows.net`：
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

如需受控執行個體和連線的詳細資訊，請參閱 [Azure SQL Database 受控執行個體連線架構](sql-database-managed-instance-connectivity-architecture.md)。
