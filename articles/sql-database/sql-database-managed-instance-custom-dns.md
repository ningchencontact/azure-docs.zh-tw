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
ms.openlocfilehash: 588fac1fc48396584188eec44f21a7005dc8ed96
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567546"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>設定 Azure SQL Database 受控執行個體的自訂 DNS

Azure SQL Database 受控執行個體必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 內。 在少數案例中 (例如，db 電子郵件，連結的伺服器連線至您雲端或混合式環境中的其他 SQL 執行個體)，必須從受控執行個體解析私人主機名稱。 在此情況下，您需要在 Azure 內設定自訂 DNS 。 

由於受控執行個體在內部工作中使用相同的 DNS, 因此您必須設定自訂 DNS 伺服器, 使其可以解析公用功能變數名稱。

   > [!IMPORTANT]
   > 一律針對郵件伺服器、SQL Server 和其他服務使用完整網域名稱 (FQDN)，即使它們位在您的私人 DNS 區域內。 例如，針對郵件伺服器使用 `smtp.contoso.com`，因為無法正確地解析簡單的 `smtp`。

   > [!IMPORTANT]
   > 更新虛擬網路 DNS 伺服器不會立即影響受控執行個體。 受控執行個體 DNS 設定會在 DHCP 租用到期或平臺 upgarade 之後更新, 但會先產生任何內容。 **建議使用者在建立其第一個受控執行個體之前, 先設定其虛擬網路 DNS 設定。**

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何新建受控執行個體的教學課程，請參閱[建立受控執行個體](sql-database-managed-instance-get-started.md)。
- 如需有關設定受控執行個體 VNet 的詳細資訊，請參閱[受控執行個體 VNet 組態](sql-database-managed-instance-connectivity-architecture.md)
