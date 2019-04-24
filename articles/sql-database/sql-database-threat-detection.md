---
title: 威脅偵測 - Azure SQL Database | Microsoft Docs
description: 威脅偵測會偵測異常資料庫活動，指出單一資料庫或彈性集區中的資料庫有潛在的安全性威脅。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331466"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>適用於單一或集區資料庫的 Azure SQL Database 威脅偵測

適用於單一和集區資料庫的[威脅偵測](sql-database-threat-detection-overview.md)會偵測意圖存取或惡意探索資料庫，並可能造成損害的異常活動。 威脅偵測可識別**潛在 SQL 插入式攻擊**、**來自不尋常位置或資料中心的存取**、**來自不熟悉主體或疑似有害應用程式的存取**及**暴力 SQL 認證** - 請參閱[威脅偵測警示](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)以取得更多詳細資料。

您可以透過[電子郵件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 入口網站](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收偵測到威脅的通知

[威脅偵測](sql-database-threat-detection-overview.md)是[進階資料安全性](sql-database-advanced-data-security.md) (ADS) 供應項目的一部分，該供應項目是進階 SQL 安全性功能的整合套件。 威脅偵測可以透過中央 SQL ADS 入口網站存取及管理。 進階資料安全性套件的費用是每部邏輯伺服器每月 15$，前 30 天免費。

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>使用 Azure 入口網站為資料庫設定威脅偵測

1. 在 [https://portal.azure.com](https://portal.azure.com) 上啟動 Azure 入口網站。
2. 瀏覽至所要保護的 Azure SQL Database 伺服器的組態頁面。 在 [安全性] 設定中，選取 [進階資料安全性]。
3. 在 [進階資料安全性] 組態頁面上：

   - 在伺服器上啟用進階資料安全性。
   - 在 [威脅偵測設定] 的 [傳送警示給] 文字方塊中，提供要在偵測到異常資料庫活動時收到安全性警示的電子郵件清單。
  
   ![設定威脅偵測](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>使用 PowerShell 設定威脅偵測

如需指令碼範例，請參閱[使用 PowerShell 設定稽核與威脅偵測](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解[威脅偵測](sql-database-threat-detection-overview.md)。
- 深入了解[受控執行個體中的威脅偵測](sql-database-managed-instance-threat-detection.md)。  
- 深入了解[進階資料安全性](sql-database-advanced-data-security.md)。
- 深入了解[稽核](sql-database-auditing.md)
- 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 如需有關定價的詳細資訊，請參閱 [SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/)  
