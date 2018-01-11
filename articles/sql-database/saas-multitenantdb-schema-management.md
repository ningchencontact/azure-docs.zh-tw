---
title: "在多租用戶應用程式中管理 Azure SQL Database 結構描述 | Microsoft Docs"
description: "在使用 Azure SQL Database 的多租用戶應用程式中，管理多租用戶的結構描述"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 135764a7d89dcf711ff7fe9416850f1af9329479
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2018
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>在使用 Azure SQL Database 的多租用戶應用程式中，管理多租用戶的結構描述

本教學課程會檢查為雲端服務 (SaaS) 應用程式維護在軟體中的資料庫可能會大量以及挑戰。 解決方案會示範用於管理的開發和實作應用程式的生命期限內的結構描述增強功能。

隨著任何應用程式的發展，變更其資料表資料行或其他結構描述，或其參考資料，可能會發生，或效能相關的項目。 使用 SaaS 應用程式時，這些變更必須部署在協調的方式跨多個現有的租用戶資料庫。 而且這些變更必須包含在未來的租用戶資料庫將會加入至應用程式。 因此，變更也必須加入到新的資料庫會佈建程序。

#### <a name="two-scenarios"></a>兩個案例

本教學課程中，瀏覽下列兩種案例：
- 將參考資料更新部署的所有租用戶。
- Retuning 包含參考資料的資料表上的索引。

[彈性作業](sql-database-elastic-jobs-overview.md)Azure SQL Database 的功能用來跨租用戶資料庫執行這些作業。 標準範本租用戶資料庫上也操作工作。 佈建新資料庫時，會使用此範本。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立工作帳戶。
> * 跨多個租用戶進行查詢。
> * 更新所有租用戶資料庫中的資料。
> * 租用戶的所有資料庫中的資料表上建立索引。

## <a name="prerequisites"></a>必要條件

- Wingtip 票證應用程式必須已部署：
    - 如需指示，請參閱第一個教學課程中，這會帶來*Wingtip 票證*SaaS 的多租戶資料庫應用程式：<br />[部署及瀏覽的分區化的多租用戶應用程式使用 Azure SQL Database](saas-multitenantdb-get-started-deploy.md)。
        - 五分鐘內部署程序執行。
    - 您必須擁有*分區化的多租用戶*Wingtip 安裝的版本。 適用於版本*獨立*和*每一租用戶資料庫*不支援目前的教學課程。

- 必須安裝最新版本的 SQL Server Management Studio (SSMS)。 [下載並安裝 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。

- 必須安裝 azure PowerShell。 如需詳細資訊，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

> [!NOTE]
> 本教學課程使用的 Azure SQL Database 服務處於有限預覽版本的功能 ([彈性資料庫工作](sql-database-elastic-database-client-library.md))。 如果您想要進行此教學課程，提供您的訂用帳戶識別碼以 *SaaSFeedback@microsoft.com* 主旨 = 彈性作業預覽。 在您收到訂用帳戶已啟用的確認之後，請[下載並安裝最新的發行前版本作業 Cmdlet (英文)](https://github.com/jaredmoo/azure-powershell/releases)。 這個預覽是有限，因此連絡 *SaaSFeedback@microsoft.com* 相關的問題或支援。

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 結構描述管理模式的簡介

此範例中使用的分區化的多租用戶資料庫模型可讓租用戶資料庫包含一或多個租用戶。 這個範例會探索可能會使用多租用戶和一個租用戶的資料庫，啟用混合*混合式*租用戶管理模型。 管理這些資料庫很複雜。 [彈性作業](sql-database-elastic-jobs-overview.md)有助於 SQL 資料層的管理。 作業可讓您安全可靠地執行 TRANSACT-SQL 指令碼工作處理，對租用戶資料庫的群組。 工作是與使用者互動或輸入無關。 這個方法可以用來部署應用程式中的所有租用戶之間變更結構描述或一般的參考資料。 彈性的工作也可用來維護資料庫的標準範本複本。 範本用來建立新的租用戶，永遠確保最新的結構描述和參考資料是使用中。

![畫面](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>彈性作業有限預覽版

沒有新版本的彈性現在是 Azure SQL Database 的整合式的功能的工作。 整合式的意思是它不需要其他服務或元件。 這個新的「彈性作業」版本目前處於有限預覽版狀態。 有限預覽版本目前支援 PowerShell 建立工作帳戶及 T-SQL 來建立及管理工作。

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>取得 Wingtip Tickets SaaS 多租用戶資料庫應用程式原始碼和指令碼

Wingtip 票證 SaaS 多租用戶資料庫指令碼和應用程式的原始程式碼可用[WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) Github 上的儲存機制。 請參閱[一般指引](saas-tenancy-wingtip-app-guidance-tips.md)的步驟來下載和解除封鎖 Wingtip 票證 SaaS 指令碼。 

## <a name="create-a-job-account-database-and-new-job-account"></a>建立作業帳戶資料庫和新的作業帳戶

本教學課程需要您使用 PowerShell 建立的工作帳戶的資料庫和工作帳戶。 MSDB 資料庫中使用的 SQL 代理程式，例如彈性的工作會使用 Azure SQL database 儲存工作定義、 工作狀態和歷程記錄。 建立工作帳戶之後，您可以建立並立即監視作業。

1. 在**PowerShell ISE**，開啟*...\\學習模組\\結構描述管理\\示範 SchemaManagement.ps1*。
2. 按 **F5** 以執行指令碼。

*示範 SchemaManagement.ps1*指令碼會呼叫*部署 SchemaManagement.ps1*指令碼來建立層*S2*資料庫名為**jobaccount**類別目錄伺服器上。 指令碼，然後建立工作帳戶，將 jobaccount 資料庫做為參數傳遞給工作的帳戶建立呼叫。

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>建立作業以將新的參考資料部署到所有租用戶

#### <a name="prepare"></a>準備

每個租用戶資料庫包含一組地點中的型別**VenueTypes**資料表。 法院類型定義事件裝載在法院的種類。 在此練習中，您將更新部署至所有資料庫，以加入兩個其他地點型別：*機車賽*和*游泳社團*。 這些場地類型會對應至您在租用戶活動應用程式中看到的背景影像。

部署新的參考資料之前，請注意法院類型已存在的數目，這可能是 10。 按一下下列連結來 Wingtip web UI，然後再按一下 take 便箋**法院類型**下拉功能表：
- http://events.wingtip-mt.<USER>.trafficmanager.net

現在您可以計算原始法院類型數目。 特別是，都請注意，*機車賽*也*游泳社團*尚未存在。

#### <a name="steps"></a>步驟

現在建立工作以更新**VenueTypes**資料庫資料表中每個租用戶，加上兩個新的法院類型。

若要建立新的工作，您可以使用作業的系統預存程序中所建立的一組*jobaccount*資料庫。 建立工作帳戶時，所建立的程序。

1. 在 SSMS 中，連線到租用戶伺服器：tenants1-mt-\<user\>.database.windows.net

2. 瀏覽至*tenants1*資料庫*tenants1-mt-\<使用者\>.database.windows.net*伺服器。

3. 查詢*VenueTypes*資料表以確認*機車賽*和*游泳社團*尚不在結果清單中。

4. 連接到類別目錄伺服器，也就是*目錄-mt-\<使用者\>.database.windows.net*。

5. 連接到*jobaccount*資料庫中的類別目錄伺服器。

6. 在 SSMS 中，開啟檔案*...\\學習模組\\結構描述管理\\DeployReferenceData.sql*。

7. 修改陳述式：set @User = &lt;user&gt; ，並使用您在部署 Wingtip Tickets SaaS 多租用戶資料庫應用程式時使用的 User 值來取代。

8. 按 **F5** 以執行指令碼。

#### <a name="observe"></a>觀察

觀察下列項目中的*DeployReferenceData.sql*指令碼：

- **預存程序\_新增\_目標\_群組**建立目標群組名稱*DemoServerGroup*，並將目標成員加入至群組。

- **預存程序\_新增\_目標\_群組\_成員**新增下列項目：
    - A*伺服器*目標成員類型。
        - 這是*tenants1-mt-&lt;使用者&gt;*包含租用戶資料庫的伺服器。
        - 因此在伺服器中的所有資料庫會都包含在作業中，執行作業時。
    - A*資料庫*目標成員類型的標準資料庫 (*basetenantdb*) 位於*目錄-mt-&lt;使用者&gt;* server，
    - A*資料庫*目標成員類型包含*adhocreporting*之後的教學課程中所使用的資料庫。

- **預存程序\_新增\_作業**建立工作，稱為*部署參考資料*。

- **sp\_add\_jobstep** 會建立作業步驟，包含更新參考 VenueTypes 資料表的 T-SQL 命令文字。

- 指令碼中的其餘檢視會顯示物件是否存在，以及監視作業執行。 若要檢閱狀態的值中使用這些查詢**生命週期**資料行來判斷作業已順利完成。 作業會更新租用戶資料庫，並更新包含參考資料表的兩個其他資料庫。

在 SSMS 中，瀏覽至租用戶資料庫上*tenants1-mt-&lt;使用者&gt;*伺服器。 查詢*VenueTypes*資料表以確認*機車賽*和*游泳社團*現在會加入至資料表。 法院類型總數應由兩個已增加。

## <a name="create-a-job-to-manage-the-reference-table-index"></a>建立作業以管理參考資料表索引

這個練習會類似於上一個練習。 這個練習中會建立參考資料表主索引鍵上重建索引的工作。 索引重建是一般資料庫管理作業，系統管理員可能執行大型資料載入之後，插入資料表中，以改善效能。

1. 在 SSMS 中，連接到*jobaccount*資料庫*目錄-mt-&lt;使用者&gt;.database.windows.net*伺服器。

2. 在 SSMS 中，開啟*...\\學習模組\\結構描述管理\\OnlineReindex.sql*。

3. 按 **F5** 以執行指令碼。

#### <a name="observe"></a>觀察

觀察下列項目中的*OnlineReindex.sql*指令碼：

* **預存程序\_新增\_作業**會建立一個新的工作，稱為*線上重新建立索引的 PK\_\_VenueTyp\_\_265E44FD7FD4C885*。

* **sp\_add\_jobstep** 會建立作業步驟，其中包含要更新索引的 T-SQL 命令文字。

* 指令碼中的其餘檢視會監視作業執行。 若要檢閱狀態的值中使用這些查詢**生命週期**資料行來判斷工作已經成功完成時對所有目標群組的成員。

## <a name="additional-resources"></a>其他資源

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [管理相應放大的雲端資料庫](sql-database-elastic-jobs-overview.md)
* [建立和管理相應放大的雲端資料庫](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> - 建立要跨多個租用戶進行查詢的工作帳戶。
> - 更新所有租用戶資料庫中的資料。
> - 租用戶的所有資料庫中的資料表上建立索引。

接下來請嘗試[臨機操作報告教學課程](saas-multitenantdb-adhoc-reporting.md)。

