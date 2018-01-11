---
title: "SQL Database 災害復原演練 | Microsoft Docs"
description: "使用 Azure SQL Database 來執行災害復原鑽研的學習指引和最佳做法。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 10/20/2016
ms.workload: Inactive
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 73c2cbe978c980cbe1269b34cdb9f5ff86113e61
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="performing-disaster-recovery-drill"></a>執行災害復原演練
建議您定期驗證應用程式的復原工作流程整備。 最佳設計作法是，驗證容錯移轉所涉及之資料遺失和 (或) 中斷的應用程式行為和影響。 這也是大多數業界標準在商務持續性認證中的規定。

災害復原演練內容包括：

* 模擬資料層中斷情況
* 復原
* 驗證復原後的應用程式完整性

執行演練的工作流程會因您 [設計商務持續性之應用程式](sql-database-business-continuity.md)的方式而異。 本文說明的最佳作法進行災害復原訓練，Azure SQL Database 的內容中。

## <a name="geo-restore"></a>異地還原
為了避免潛在資料遺失進行災害復原訓練時，執行建立實際執行環境的複本，並使用它來驗證應用程式的容錯移轉工作流程中使用測試環境下鑽研。

#### <a name="outage-simulation"></a>中斷模擬
若要模擬中斷，您可以重新命名來源資料庫。 這會導致應用程式連線失敗。

#### <a name="recovery"></a>復原
* 如[此處](sql-database-disaster-recovery.md)所述，將資料庫異地還原至其他伺服器。
* 將應用程式組態變更為連接到復原資料庫，並遵循[在復原後設定資料庫](sql-database-disaster-recovery.md)指南以完成復原。

#### <a name="validation"></a>驗證
* 驗證 （包括連接字串、 登入、 基本功能測試或驗證的其他部分標準應用程式 signoffs 程序） 的應用程式完整性 post 復原完成向下切入。

## <a name="failover-groups"></a>容錯移轉群組
使用容錯移轉群組保護資料庫，向下切入練習牽涉到計劃的容錯移轉至次要伺服器。 規劃的容錯移轉可確保，主要和容錯移轉群組中的次要資料庫保持同步的角色切換時。 與未計畫的容錯移轉不同的是，這項作業不會導致資料遺失，所以可以在生產環境中執行這項演練。

#### <a name="outage-simulation"></a>中斷模擬
若要模擬中斷，您可以停用連接到資料庫的 Web 應用程式或虛擬機器。 這會導致 Web 用戶端的連線失敗。

#### <a name="recovery"></a>復原
* 請確定應用程式中的組態 DR 區域會指向次要，前者變成完全可存取的新主要複本。
* 起始[規劃的容錯移轉](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)從次要伺服器的容錯移轉群組。
* 請遵循 [在復原後設定資料庫](sql-database-disaster-recovery.md) 指南以完成復原。

#### <a name="validation"></a>驗證
驗證 （包括連線、 基本功能測試，或其他所需的向下切入 signoffs 的驗證） 的應用程式完整性 post 復原完成向下切入。

## <a name="next-steps"></a>後續步驟
* 若要深入了解業務持續性案例，請參閱[持續性案例](sql-database-business-continuity.md)。
* 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)
* 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
* 若要深入了解更快速的復原選項，請參閱[作用中地理複寫和容錯移轉群組](sql-database-geo-replication-overview.md)。  
