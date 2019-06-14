---
title: 探索 Azure SQL Database 受控執行個體管理端點 | Microsoft Docs
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
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699977"
---
# <a name="determine-the-management-endpoint-ip-address"></a>判斷管理端點 IP 位址

Azure SQL Database 受控執行個體的虛擬叢集包含 Microsoft 用來進行管理作業的管理端點。 管理端點會受到網路層級內建防火牆和應用程式層級的相互憑證驗證所保護。 您可以判斷管理端點的 IP 位址，但您無法存取此端點。

## <a name="determine-ip-address"></a>判斷 IP 位址

讓我們假設受控執行個體的主機為 `mi-demo.xxxxxx.database.windows.net`。 使用主機名稱執行 `nslookup`。

![解析內部主機名稱](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

現在執行另一個`nslookup`反白顯示的名稱移除`.vnet.`區段。 當您執行此命令時，您將取得的公用 IP 位址。

![解析公用 IP 位址](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>後續步驟

如需受控執行個體和連線的詳細資訊，請參閱 [Azure SQL Database 受控執行個體連線架構](sql-database-managed-instance-connectivity-architecture.md)。
