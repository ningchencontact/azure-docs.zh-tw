---
title: Azure SQL Database 受控執行個體自訂 DNS | Microsoft Docs
description: 本主題描述 Azure SQL Database 受控執行個體的自訂 DNS 設定選項。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9b15ebc40e99c1cd454396ccde5cca6b1a46abbc
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244753"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>設定 Azure SQL Database 受控執行個體的自訂 DNS

Azure SQL Database 受控執行個體必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 內。 在少數案例中 (例如，db 電子郵件，連結的伺服器連線至您雲端或混合式環境中的其他 SQL 執行個體)，必須從受控執行個體解析私人主機名稱。 在此情況下，您需要在 Azure 內設定自訂 DNS 。 

由於受控執行個體在內部工作中使用相同的 DNS，因此請設定自訂 DNS 伺服器，讓它可以解析公用功能變數名稱。

> [!IMPORTANT]
> 請一律使用郵件伺服器、SQL Server 實例及其他服務的完整功能變數名稱（FQDN），即使它們是在您的私人 DNS 區域內也一樣。 例如，將 `smtp.contoso.com` 用於郵件伺服器，因為 `smtp` 將無法正確解析。 建立參考相同虛擬網路內 SQL Vm 的連結伺服器或複寫也需要 FQDN 和預設 DNS 尾碼。 例如，`SQLVM.internal.cloudapp.net`。 如需詳細資訊，請參閱[使用您自己的 DNS 伺服器的名稱解析](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)。

> [!IMPORTANT]
> 更新虛擬網路 DNS 伺服器不會立即影響受控執行個體。 受控執行個體 DNS 設定會在 DHCP 租用到期或平臺 upgarade 之後更新（以先發生者為准）。 **建議使用者在建立其第一個受控執行個體之前，先設定其虛擬網路 DNS 設定。**

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何新建受控執行個體的教學課程，請參閱[建立受控執行個體](sql-database-managed-instance-get-started.md)。
- 如需有關設定受控執行個體 VNet 的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)
