---
title: Azure SQL Database 匯入/匯出服務需要很長的時間來匯入或匯出資料庫 |Microsoft Docs
description: Azure SQL Database 匯入/匯出服務需要很長的時間來匯入或匯出資料庫
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974457"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database 匯入/匯出服務需要很長的時間來匯入或匯出資料庫

使用 Azure SQL Database 匯入/匯出服務時，您可能會注意到，有時候程式可能需要很長的時間才能完成。 本文提供三個延遲的可能原因，以及您可以用來解決這些問題的替代方法的其他資訊。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database 匯入/匯出服務

Azure SQL Database 匯入/匯出服務是以 REST 為基礎的 web 服務，會在每個 Microsoft Azure 的資料中心執行。 這是當您使用 [匯[入資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal)] 或 [[匯出](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal)] 選項來移動 Microsoft Azure 入口網站中的 SQL 資料庫時，所呼叫的服務。 此服務提供免費的要求佇列服務和免費的計算服務，可執行從 Microsoft Azure SQL database 到 Microsoft Azure 二進位大型物件（BLOB）儲存體的匯入和匯出。

匯入和匯出作業不是傳統的實體資料庫備份，而是使用特殊 BACPAC 格式之資料庫的邏輯備份。 這個邏輯 BACPAC 格式可讓您避免必須使用可能因 SQL Server 和 SQL Database 版本不同的實體格式。 因此，您可以使用它來安全地將資料庫還原至 SQL 資料庫和 SQL Server 資料庫。

## <a name="what-causes-the-process-to-take-a-long-time"></a>造成進程花很長時間的原因

Azure SQL Database 的匯入/匯出服務會為每個區域提供有限數量的計算虛擬機器（Vm），以處理匯入和匯出作業。 計算 VM 會每個區域託管，以確保匯入或匯出可避免跨區域頻寬延遲和收費。 因此，如果在同一個區域中同時進行太多要求，處理作業時就會發生明顯的延遲。 完成要求所需的時間可能會因數秒到數小時而有所不同。

> [!NOTE]
> 如果要求未在四天內處理，服務就會自動取消要求。

## <a name="recommended-solutions"></a>建議的解決方案

如果您的資料庫匯出僅用於復原意外刪除的資料，所有 Azure SQL Server 資料庫版本都會從系統產生的備份中提供自助式還原功能。 但是，如果您因為其他原因而需要這些匯出，而且您需要持續更快或更容易預測的匯入或匯出效能，則應該考慮下列選項：

* [使用 SQLPackage 公用程式匯出到 BACPAC 檔案](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [使用 SQL Server Management Studio （SSMS）匯出至 BACPAC 檔案](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* 使用 Microsoft® SQL Server®資料層應用程式架構（DacFx） API，直接在您的程式碼中執行 BACPAC 匯入或匯出。 如需其他資訊，請參閱
  * [匯出資料層應用程式](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft SqlServer Dac 命名空間](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [下載 DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>匯出或匯入 Azure SQL 資料庫時的考慮

* 本文中討論的所有方法都會使用 DTU 配額，而導致 Azure SQLDB 服務進行節流。 您可以[在 Azure 入口網站上查看資料庫的 DTU 統計資料](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance)。 如果資料庫達到資源限制，請[升級服務層](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)來新增更多資源。
* 用戶端應用程式（例如 sqlpackage 公用程式或您的自訂 DAC 應用程式）最好是從與您的 SQL 資料庫相同的區域中的虛擬機器（VM）執行，否則您可能會因為網路延遲而遇到效能問題。
* 匯出不含叢集索引的大型資料表可能會很慢，或甚至導致失敗。 這是因為資料表無法以平行方式分割和匯出，而且必須在單一交易中匯出，而這會導致匯出期間的緩慢和潛在失敗，特別是針對大型資料表。 


## <a name="related-documents"></a>相關檔

[匯出 Azure SQL 資料庫時的考慮](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
