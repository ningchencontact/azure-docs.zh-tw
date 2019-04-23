---
title: 設定威脅偵測 - Azure SQL Database 受控執行個體 | Microsoft Docs
description: 威脅偵測會偵測異常資料庫活動，指出受控執行個體中的資料庫有潛在的安全性威脅。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a8e9dfe70e300e6b1d0d50aae60660644f2ab31d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794553"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>在 Azure SQL Database 受控執行個體中設定威脅偵測 (預覽)

適用於[受控執行個體](sql-database-managed-instance-index.yml)的[威脅偵測](sql-database-threat-detection-overview.md)會偵測意圖存取或惡意探索資料庫，並可能會造成損害的異常活動。 威脅偵測可識別**潛在 SQL 插入式攻擊**、**來自不尋常位置或資料中心的存取**、**來自不熟悉主體或疑似有害應用程式的存取**及**暴力 SQL 認證** - 請參閱[威脅偵測警示](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)以取得更多詳細資料。

您可以透過[電子郵件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 入口網站](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收偵測到威脅的通知

[威脅偵測](sql-database-threat-detection-overview.md)是[進階資料安全性](sql-database-advanced-data-security.md) (ADS) 供應項目的一部分，該供應項目是進階 SQL 安全性功能的整合套件。 威脅偵測可以透過中央 SQL ADS 入口網站存取及管理。 威脅偵測服務的費用是每個受控執行個體每月 15 美元，前 30 天免費。

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>使用 Azure 入口網站為受控執行個體設定威脅偵測

1. 在 [https://portal.azure.com](https://portal.azure.com) 上啟動 Azure 入口網站。
2. 瀏覽至您要保護的受控執行個體組態頁面。 在 [設定] 頁面中，選取 [威脅偵測]。
3. 在威脅偵測組態頁面中
   - [開啟]  威脅偵測。
   - 設定在偵測到異常資料庫活動時收到安全性警示的**電子郵件清單**。
   - 選取儲存異常威脅稽核記錄的 [Azure 儲存體帳戶]。
4. 按一下 [儲存] ，以儲存新的或更新的威脅偵測原則。

   ![威脅偵測](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>後續步驟

- 深入了解[威脅偵測](sql-database-threat-detection-overview.md)。
- 若要了解受控執行個體，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 深入了解[單一資料庫的威脅偵測](sql-database-threat-detection.md)。
- 深入了解[受控執行個體稽核](https://go.microsoft.com/fwlink/?linkid=869430)。
- 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)。
