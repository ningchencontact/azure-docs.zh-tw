---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198056"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- 置於[邏輯伺服器](https://docs.microsoft.com/azure/sql-database/sql-database-single-index)或[受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)上的 Azure SQL Database。 您可以使用下列其中一個技術來建立資料庫：

| 邏輯伺服器 | 受控執行個體 |
| --- | --- |
| [入口網站](../articles/sql-database/sql-database-get-started-portal.md) | [入口網站](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [CLI](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **僅限邏輯伺服器** - 一項已設定而可讓您連線至邏輯伺服器的伺服器層級防火牆規則。 如需詳細資訊，請參閱[建立伺服器層級防火牆規則](../articles/sql-database/sql-database-get-started-portal-firewall.md)。
- **僅限受控執行個體** - 已設定會從存取受控執行個體的電腦進行的連線。 您可以使用下列選項：
  - 受控執行個體所在的相同 Azure VNet 之中可存取執行個體的 [Azure 虛擬機器](../articles/sql-database/sql-database-managed-instance-configure-vm.md)。
  - 電腦上可讓您將電腦加入受控執行個體所在的 VNet，並使用受控執行個體作為網路中任何其他 SQL Server 的[點對站連線](../articles/sql-database/sql-database-managed-instance-configure-p2s.md)。
