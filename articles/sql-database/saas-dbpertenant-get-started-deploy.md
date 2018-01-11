---
title: "Azure SQL Database-資料庫每個租用戶 SaaS 教學課程 |Microsoft 文件"
description: "部署及探索 Wingtip Tickets SaaS 多租用戶應用程式，其使用 Azure SQL Database 示範每一租用戶一個資料庫和其他 SaaS 模式。"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: 5342b5290fab9826a2b38cd7ada63a6736c77601
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>使用 Azure SQL Database，部署及探索使用 每一租用戶一個資料庫模式的多租用戶 SaaS 應用程式

在本教學課程中，您在部署及瀏覽 Wingtip 票證 SaaS*每一租用戶資料庫*應用程式 (Wingtip)。 應用程式會使用每個租用戶模式中，資料庫來儲存多個租用戶的資料。 應用程式被設計來展示功能的 Azure SQL Database，可以簡化啟用 SaaS 案例。

按一下 [藍色] 按鈕後的五分鐘**部署至 Azure**，您有多租用戶 SaaS 應用程式。 應用程式包括 Microsoft Azure 雲端中執行 Azure SQL Database。 應用程式會部署與三個範例租用戶，每個都有它自己的資料庫。 所有資料庫都部署到 SQL*彈性集區*。 應用程式會部署到您的 Azure 訂用帳戶。 您擁有完整存取權瀏覽和使用應用程式的個別元件。 應用程式 C# 原始程式碼和管理指令碼，可用[WingtipTicketsSaaS DbPerTenant GitHub 儲存機制][github-wingtip-dpt]。

您會在本教學課程中學到：

> [!div class="checklist"]
> - 如何部署 Wingtip SaaS 應用程式。
> - 在何處取得應用程式原始程式碼和管理指令碼。
> - 關於伺服器、 集區和構成應用程式的資料庫。
> - 如何使用「目錄」將租用戶對應到其資料。
> - 如何佈建新租用戶。
> - 如何監視應用程式中的租用戶活動。

A[相關教學課程系列](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)詢問您是否要探索各種 SaaS 設計和管理模式。 教學課程建置超出此初始的部署。
當您使用這些教學課程時，您可以查看如何檢查提供的指令碼來實作不同 SaaS 模式。
指令碼會示範如何功能的 SQL Database 簡化 SaaS 應用程式的開發。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定已完成下列必要條件：

* 已安裝 Azure PowerShell。 如需詳細資訊，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

## <a name="deploy-the-wingtip-tickets-saas-application"></a>部署 Wingtip Tickets SaaS 應用程式

部署應用程式：

1. 選擇，請記住您需要下列參數的值：

    - **使用者**： 選擇較短的值，例如您後面接著數字的簡稱。 例如， *af1*。 使用者參數可以包含字母、 數字和連字號 （無空格）。 第一個和最後一個字元必須是字母或數字。 我們建議所有字母都是小寫。
    - **資源群組**： 每次部署 Wingtip 應用程式時，您必須選擇不同的唯一名稱，新的資源群組。 我們建議您將使用者名稱附加至資源群組的基底名稱。 範例資源群組名稱可能是*wingtip af1*。 同樣地，我們建議所有字母都是小寫。

2. 在 Azure 入口網站中，開啟 Wingtip 票證 SaaS 資料庫，每個租用戶部署範本，依序按一下藍色**部署至 Azure**  按鈕。

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

3. 在範本中，輸入必要的參數值：

    > [!IMPORTANT]
    > 為了示範的目的，已刻意將某些驗證和伺服器防火牆設為不安全。 我們建議您*建立新的資源群組*。 不要使用現有資源群組、伺服器或集區。 請不要將此應用程式、指令碼或任何部署的資源用於生產環境。 不使用應用程式之後請刪除此資源群組，以停止相關計費。

    - **資源群組**-選取**建立新**並提供唯一**名稱**您先前選擇的資源群組。 
    - **位置** - 從下拉式清單中選取 [位置]。
    - **使用者**-使用您先前選擇的使用者名稱值。

4. 部署應用程式。

    - 按一下以同意條款和條件。
    - 按一下 [購買]。

5. 監視部署狀態，依序按一下**通知**，這是鈴鐺圖示右邊的 [搜尋] 方塊。 部署 Wingtip Tickets SaaS 應用程式大約需要五分鐘。

   ![部署成功](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>下載並解除封鎖 Wingtip Tickets 管理指令碼

部署應用程式時，請下載原始程式碼和管理指令碼。

> [!IMPORTANT]
> Windows 可能會封鎖可執行檔的內容 （指令碼、 Dll），當從外部來源下載.zip 檔案並擷取。 從 zip 檔案解壓縮指令碼時，請遵循下列步驟先解除封鎖 .zip 檔案，再進行解壓縮。 解除封鎖，可確保指令碼可以執行。

1. 瀏覽至[WingtipTicketsSaaS DbPerTenant GitHub 儲存機制][github-wingtip-dpt]。
2. 按一下 [複製或下載]。
3. 按一下**Download ZIP**，然後儲存檔案。
4. 以滑鼠右鍵按一下**WingtipTicketsSaaS-DbPerTenant-master.zip**檔案，然後再選取**屬性**。
5. 在**一般**索引標籤上，選取**解除封鎖**，然後按一下 **套用**。
6. 按一下 [SERVICEPRINCIPAL] 。
7. 將檔案解壓縮。

指令碼位於 *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules* 資料夾中。

## <a name="update-the-user-configuration-file-for-this-deployment"></a>更新此部署的使用者組態檔

執行任何指令碼之前，請在 **UserConfig.psm1** 中更新「資源群組」和「使用者」值。 將這些變數設定為您在部署期間使用的值。

1. 在 [PowerShell ISE] 中開啟 ...\\Learning Modules\\*UserConfig.psm1* 
2. 將 ResourceGroupName 和 Name 更新為您部署的特定值 (只在第 10 行和第 11 行)。
3. 儲存變更！

幾乎每一個指令碼中都會參考這些數值。

## <a name="run-the-application"></a>執行應用程式

應用程式會示範裝載事件的場地。 場地類型包括音樂廳、爵士俱樂部和運動俱樂部。 Wingtip 票證管道會註冊為租用戶。 成為租用戶讓場地能夠輕鬆列出活動，並對其客戶銷售票券。 每個場地都有客製化網站可列出其活動及售票。

內部應用程式中每個租用戶取得 SQL 資料庫部署到 SQL 彈性集區。

中央 [事件中樞] 頁面提供部署中租用戶的連結清單。

1. 在您的瀏覽器中開啟 [事件中樞]：http://events.wingtip-dpt.&lt;USER&gt;.trafficmanager.net (請以您部署的使用者值取代&lt;USER&gt;)：

    ![事件中樞](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. 按一下 [Events Hub (活動中心)] 中的 [Fabrikam Jazz Club (Fabrikam 爵士俱樂部)]。

    ![活動](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Azure 流量管理員

Wingtip 應用程式會使用[ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md)控制所分配的連入要求。 若要存取某個租用戶的事件中樞的 URL 必須遵守下列格式：

- http://events.wingtip-dpt。&lt;使用者&gt;.trafficmanager.net/fabrikamjazzclub

下表說明上述格式的組件。

| URL 的一部分 | 說明 |
| :------- | :---------- |
| http://events.wingtip-dpt | Wingtip 應用程式事件組件。<br /><br />***-dpt***部分區別*資料庫每個租用戶*Wingtip 實作與其他稍微不同的實作。 例如，文件中的其他文件提供的 Wingtip *Standalong* (*-sa*)，或*多租用戶 DB*。 |
| .*&lt;使用者&gt;* | *af1*在我們的範例。 |
| .trafficmanager.net/ | Azure 流量管理員 中，基底 URL。 |
| fabrikamjazzclub | 名為租用戶*Fabrikam Jazz 社團*。 |
| &nbsp; | &nbsp; |

1. 租用戶名稱是從 URL，請剖析事件的應用程式。
2. 租用戶名稱用來建立索引鍵。
3. 索引鍵用來存取的類別目錄中取得的租用戶資料庫的位置。
    - 目錄藉由使用*分區對應管理*。
4. *事件中樞*目錄中會使用擴充的中繼資料，以取得事件的 Url 清單。

在實際執行環境中，通常您會建立一筆 CNAME DNS 記錄[*點將公司網際網路網域*](../traffic-manager/traffic-manager-point-internet-domain.md)到流量管理員設定檔。

## <a name="start-generating-load-on-the-tenant-databases"></a>開始在租用戶資料庫上產生負載

應用程式已部署完成，請立即使用！
*Demo-LoadGenerator* PowerShell 指令碼會啟動對所有租用戶資料庫執行的工作負載。
許多 SaaS 應用程式的真實世界負載是偶發和無法預期。
若要模擬這種類型的負載，產生器會產生隨機的尖峰或活動在每個租用戶上暴增的負載。
暴增會隨機間隔發生。
花幾分鐘的時間要出現時的負載模式。 因此，可以讓執行至少三或四分鐘，再監視負載的產生器。

1. 在 [PowerShell ISE] 中，開啟 ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* 指令碼。
2. 按**F5**執行指令碼，並啟動載入器。 （保留預設的參數值現在。）

針對可能的重新執行以外的任何不要重複使用相同的 PowerShell ISE 執行個體*示範 LoadGenerator.ps1*。 如果您需要執行其他的 PowerShell 指令碼，請啟動個別的 PowerShell ISE。

#### <a name="rerun-with-different-parameters"></a>重新執行使用不同的參數

如果您想要重新執行工作負載測試使用不同的參數，請遵循下列步驟：

1. 停止*LoadGenerator.ps1*。
    - 使用**Ctrl + C**，或按一下**停止** 按鈕。
    - 此作業停止不停止或影響仍在執行任何不完整的背景工作。

2. 請重新執行*示範 LoadGenerator.ps1*。
    - 這個重新執行第一次會停止任何背景工作，可能仍在執行*sp_CpuLoadGenerator*。

或者，您可以終止 PowerShell ISE 的執行個體，它會阻止任何背景工作。 啟動 PowerShell ISE 的新執行個體，然後重新執行*示範 LoadGenerator.ps1*。

#### <a name="monitor-the-background-jobs"></a>監視背景工作

如果您想要控制和監視的背景工作，您可以使用下列 cmdlet:

- Get-job
- 接收作業
- 停止工作

#### <a name="demo-loadgeneratorps1-actions"></a>示範 LoadGenerator.ps1 動作

*示範 LoadGenerator.ps1*模擬客戶交易作用中的工作負載。 下列步驟描述動作的順序，*示範 LoadGenerator.ps1*起始：

1. *示範 LoadGenerator.ps1*啟動*LoadGenerator.ps1*在前景。
    - 兩個.ps1 檔案會儲存在資料夾下*學習模組\\公用程式\\*。

2. *LoadGenerator.ps1*註冊在目錄中的所有租用戶資料庫中執行迴圈。

3. 每個租用戶資料庫*LoadGenerator.ps1*開始執行的 TRANSACT-SQL 預存程序命名為*sp_CpuLoadGenerator*。
    - 執行作業會在背景中啟動藉由呼叫*Invoke SqlAzureWithRetry* cmdlet。
    - *sp_CpuLoadGenerator*的預設持續時間為 60 秒的 SQL SELECT 陳述式前後的迴圈。 選取的問題之間的時間間隔會根據參數值而有所不同。
    - 這個.sql 檔案會儲存在下*WingtipTenantDB\\dbo\\StoredProcedures\\*。

4. 每個租用戶資料庫*LoadGenerator.ps1*也會啟動*Start-job* cmdlet。
    - *Start-job*模擬的票證銷售工作負載。

5. *LoadGenerator.ps1*繼續執行且監視新的租用戶佈建。

&nbsp;

後再繼續下一節，將保留在工作叫用的狀態下執行的負載產生器。

## <a name="provision-a-new-tenant"></a>佈建新租用戶

初始的部署會建立三個範例租用戶。 現在您可以建立另一個租用戶，若要查看已部署的應用程式上產生的影響。 Wingtip 應用程式中說明新的租用戶佈建工作流程[佈建和類別目錄的教學課程](saas-dbpertenant-provision-and-catalog.md)。 在這個階段中，您可以建立新的租用戶，小於 1 分鐘。

1. 在 [PowerShell ISE] 中，開啟 ...*Learning Modules\Provision and Catalog*\\Demo-ProvisionAndCatalog.ps1\\。
2. 按 **F5** 以執行指令碼。 （目前保留的預設值）。

   > [!NOTE]
   > 許多 Wingtip SaaS 指令碼都會使用 *$PSScriptRoot* 來瀏覽資料夾，以在其他指令碼中呼叫函式。 按 **F5** 執行完整的指令碼時，才會評估這個變數。  反白顯示和執行的選擇與**F8**可能會導致錯誤。 因此按下執行指令碼**F5**。

新的租用戶資料庫是：

- SQL 彈性集區中建立。
- 初始化。
- 在目錄中註冊。

成功佈建之後，*事件*新的租用戶的網站會出現在您的瀏覽器：

![新租用戶](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

重新整理*事件中樞*来出現在清單中新的租用戶。

## <a name="explore-the-servers-pools-and-tenant-databases"></a>探索伺服器、集區和租用戶資料庫

既然您已經開始對租用戶集合執行負載，讓我們看看已部署的一些資源：

1. 在[Azure 入口網站](http://portal.azure.com)，瀏覽至您的 SQL 伺服器清單，並開啟**目錄-dpt-&lt;使用者&gt;**伺服器。
    - 目錄伺服器包含兩個資料庫：**tenantcatalog** 和 **basetenantdb** (複製來建立新租用戶的範本資料庫)。

   ![資料庫](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. 返回至您的 SQL 伺服器清單。

3. 開啟**tenants1-dpt-&lt;使用者&gt;**持有租用戶資料庫伺服器。

4. 請參閱下列項目：
    - 每個租用戶資料庫都是 50 eDTU 標準集區中的「彈性標準」資料庫。
    - *紅色楓樹賽*資料庫，這是您先前佈建的租用戶資料庫。

   ![伺服器](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>監視集區

之後*LoadGenerator.ps1*執行數分鐘，足夠的資料應該可供開始了解某些監視功能。 這些功能會建置到集區和資料庫。

瀏覽至伺服器**tenants1-dpt-&lt;使用者&gt;**，然後按一下**Pool1**來檢視集區的資源使用率。 在下列圖表中，負載產生器會執行一小時。

   ![監視集區](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- 第一個圖表，標示為**資源使用率**，顯示集區 eDTU 使用量。
- 第二個圖表會顯示前五個資料庫的 eDTU 使用量集區中。

兩個圖表說明彈性集區與 SQL Database 是適用於 SaaS 應用程式工作負載。
圖表會顯示 4 個資料庫的 edtu 數 （最多可達 40），每個激增，且所有資料庫還可以同時舒服地 50 eDTU 集區所都支援。 50 eDTU 集區可支援更高的工作負載。
如果它們已佈建為獨立資料庫，則各需要為 S2 (50 DTU) 才能支援暴增。
4 獨立 S2 資料庫的成本是幾乎 3 倍的價格的集區。
在實際情況下，SQL Database 客戶目前在 200 eDTU 集區中執行多達 500 個資料庫。
如需詳細資訊，請參閱[效能監視教學課程](saas-dbpertenant-performance-monitoring.md)。

## <a name="additional-resources"></a>其他資源

- [在 Tickets SaaS Database Per Tenant 應用程式上建置的其他教學課程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- 若要了解彈性集區，請參閱[什麼是 Azure SQL 彈性集區](sql-database-elastic-pool.md)
- 若要了解彈性作業，請參閱[*管理相應放大的雲端資料庫*](sql-database-elastic-jobs-overview.md)
- 若要了解多租用戶 SaaS 應用程式，請參閱[*多租用戶 SaaS 應用程式的設計模式*](saas-tenancy-app-design-patterns.md)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> - 如何部署 Wingtip Tickets SaaS 應用程式
> - 關於組成應用程式的伺服器、集區和資料庫
> - 租用戶會使用「類別目錄」來對應到其資料
> - 如何佈建新租用戶
> - 如何檢視集區使用率以監視租用戶活動
> - 如何刪除範例資源以停止相關計費

接下來，再試一次[佈建和類別目錄的教學課程](saas-dbpertenant-provision-and-catalog.md)。



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

