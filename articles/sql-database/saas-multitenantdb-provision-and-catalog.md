---
title: "SaaS 多租用戶 Azure 中的佈建 |Microsoft 文件"
description: "了解如何在 Azure SQL Database 多租用戶 SaaS 應用程式中佈建及編目新租用戶"
keywords: SQL Database Azure
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: MightyPen
ms.reviewer: billgib;andrela;genemi
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: billgib
ms.openlocfilehash: 42bbb6131aa71520410b22af4d74e99a63fe81cf
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>在 SaaS 應用程式中使用分區化的多租用戶 Azure SQL database 的佈建和類別目錄新增租用戶

本文章涵蓋佈建和新的租用戶的分類中*多租用戶的分區化資料庫*模型或模式。

這篇文章有兩個主要部分：

- [概念討論](#goto_2_conceptual)佈建和新的租用戶的分類。

- [教學課程](#goto_1_tutorial)，反白顯示 PowerShell 指令碼的方式來完成佈建和分類。
    - 本教學課程會使用 Wingtip 票證 SaaS 應用程式，適用於多租用戶的分區化資料庫模式。

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>資料庫的模式

本節中，以及幾更多個依照，討論分區化資料庫的多租用戶模式的概念。

在這個多租用戶分區化模型中，每個資料庫內的資料表結構描述包含在儲存租用戶資料的資料表的主索引鍵中的租用戶金鑰。 租用戶金鑰可讓每個個別的資料庫來儲存 0、 1 或多個租用戶。 分區化資料庫的使用讓您可以輕鬆支援非常大量的租用戶的應用程式系統。 一個資料庫中儲存的任何一個租用戶的所有資料。 大量的租用戶會分散到多個分區化資料庫中。 類別目錄資料庫會儲存至其資料庫的每個租用戶的對應。

#### <a name="isolation-versus-lower-cost"></a>隔離與較低的成本

具有資料庫至其本身的租用戶喜歡的休閒活動隔離的好處。 租用戶可以擁有資料庫還原到較早的日期，而不限制的其他租用戶的影響。 可以微調資料庫效能，以再次最佳化一個租用戶，而不需入侵任何與其他租用戶。 問題在於隔離成本超過它的成本與其他租用戶共用資料庫。

新的租用戶已佈建時，它可以與其他租用戶共用資料庫，或置於它自己的新資料庫。 稍後您可以改變心意，將資料庫移到其他的狀況。

相同的 SaaS 應用程式，以最佳化成本或隔離的每個租用戶中混合使用多個租用戶與單一租用戶資料庫。

   ![具有租用戶目錄的多租用戶資料庫應用程式](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>租用戶目錄模式

當您擁有每個包含至少一個租用戶的兩個或多個資料庫時，應用程式必須有方法來探索的資料庫會儲存目前的感興趣的租用戶。 類別目錄資料庫會儲存此對應。

#### <a name="tenant-key"></a>租用戶金鑰

每個租用戶，Wingtip 應用程式可以衍生是租用戶金鑰的唯一索引鍵。 應用程式從網頁 URL 中擷取租用戶名稱。 應用程式雜湊取得金鑰名稱。 應用程式使用金鑰來存取類別目錄。 類別目錄的交互參照資料庫中儲存租用戶的相關資訊。 應用程式會使用連接的資料庫資訊。 也可以使用其他租用戶金鑰的配置。

使用目錄可在佈建之後變更租用戶資料庫的名稱或位置，而不會中斷應用程式。 在多租用戶資料庫模型中，類別目錄可容納資料庫之間移動租用戶。

#### <a name="tenant-metadata-beyond-location"></a>租用戶中繼資料以外的位置

類別目錄也可能表示租用戶是否為離線進行維護或其他動作。 和類別目錄可以擴充來儲存其他租用戶或資料庫的中繼資料，例如下列項目：
- 服務層的資料庫版本。
- 資料庫結構描述版本。
- 租用戶名稱，而且其 SLA （服務層級協議）。
- 若要啟用應用程式管理、 客戶支援或 devops 程序的資訊。  

目錄也可用來就分析目的啟用跨租用戶報表、結構描述管理並擷取資料。 

### <a name="elastic-database-client-library"></a>彈性資料庫用戶端程式庫 

在 Wingtip，目錄會實作在*tenantcatalog*資料庫。 *Tenantcatalog*會使用的分區管理功能來建立[彈性資料庫用戶端程式庫 (EDCL)](sql-database-elastic-database-client-library.md)。 程式庫可讓應用程式來建立、 管理及使用*分區對應*均會儲存在資料庫中。 分區對應交互參考其分區，這表示其分區化資料庫的租用戶金鑰。

租用戶佈建期間 EDCL 函式可以用於將應用程式或 PowerShell 指令碼分區對應中建立的項目。 稍後 EDCL 函式可以用來連接到正確的資料庫。 EDCL 會快取目錄資料庫上的流量降至最低，並加快連接的程序的連接資訊。

> [!IMPORTANT]
> 請勿*不*編輯透過直接存取此類別目錄資料庫中的資料 ！ 不支援直接更新造成資料損毀的高風險。 相反地，只使用 EDCL Api 編輯對應資料。

## <a name="tenant-provisioning-pattern"></a>租用戶佈建模式

#### <a name="checklist"></a>檢查清單

當您想要佈建到現有的共用資料庫的新的租用戶時，共用資料庫的您必須詢問下列問題：
- 沒有剩餘足夠空間給新的租用戶嗎？
- 沒有具有必要的參考資料的資料表為新的租用戶，或可以將資料加入嗎？
- 它有適當的變體的基底結構描述的新的租用戶嗎？
- 它是否在新的租用戶接近的適當地理位置？
- 它是在正確的服務層的新的租用戶嗎？

當您想要隔離在自己的資料庫中新的租用戶時，您可以建立它以符合租用戶的規格。

佈建完成之後，您必須註冊在目錄中的租用戶。 最後，可以新增租用戶對應來參考適當的分區。

#### <a name="template-database"></a>範本資料庫

執行 SQL 指令碼、部署 bacpac 或複製範本資料庫來佈建資料庫。 Wingtip 應用程式範本將資料庫複製建立新的租用戶資料庫。

就像任何應用程式，Wingtip 會隨著時間持續發展。 有時候，Wingtip 需要資料庫的變更。 變更可能包括下列項目：
- 新增或變更結構描述。
- 新增或變更的參考資料。
- 例行的資料庫維護工作，以確保最佳的應用程式效能。

有了 SaaS 應用程式，這些變更需要以經過協調的方式部署到可能非常大規模的租用戶資料庫群。 變更也需要整合到佈建程序中，以供未來在租用戶資料庫中使用。 這項挑戰瀏覽中進一步[結構描述管理教學課程](saas-tenancy-schema-management.md)。

#### <a name="scripts"></a>指令碼

本教學課程中的租用戶佈建指令碼可支援兩種下列狀況：
- 租用戶佈建到現有的資料庫與其他租用戶共用 >。
- 租用戶佈建到它自己的資料庫 >。

然後會將租用戶資料初始化，並在目錄分區對應中加以註冊。 範例應用程式中包含多個租用戶的資料庫指定一個通用的名稱，例如*tenants1*或*tenants2*。 包含單一租用戶的資料庫指定租用戶的名稱。 範例中使用的特定命名慣例並非模式中的重要部分，因為使用目錄可允許將任何名稱指派給資料庫。  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>開始教學課程

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 將租用戶佈建到多租用戶資料庫
> * 將租用戶佈建到單一租用戶資料庫
> * 將一批租用戶佈建到多租用戶和單一租用戶資料庫
> * 在目錄中註冊資料庫和租用戶對應

#### <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定已完成下列必要條件：

- 已安裝 Azure PowerShell。 如需詳細資料，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- 已部署 Wingtip Tickets SaaS 多租用戶資料庫應用程式。 若要在五分鐘內完成部署，請參閱[部署及探索 Wingtip Tickets SaaS 多租用戶資料庫應用程式](saas-multitenantdb-get-started-deploy.md)

- 取得 Wingtip 指令碼和原始程式碼：
    - 可在 [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub 存放庫中使用 Wingtip Tickets SaaS 多租用戶資料庫指令碼和應用程式來源程式碼。
    - 請參閱[一般指引](saas-tenancy-wingtip-app-guidance-tips.md)的步驟來下載和解除封鎖 Wingtip 指令碼。 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>佈建到資料庫的租用戶*共用*與其他租用戶

在本節中，您會看到一份 PowerShell 指令碼所採取的動作主要用於佈建。 然後您可以使用 PowerShell ISE 偵錯工具逐步執行指令碼，以查看在程式碼中的動作。

#### <a name="major-actions-of-provisioning"></a>主要動作的佈建

以下是您逐步執行佈建工作流程的重要元素：

- **計算新的租用戶金鑰**： 雜湊函式用於從租用戶的名稱建立租用戶金鑰。
- **檢查租用戶金鑰已經存在**： 類別目錄進行檢查以確保已經尚未登錄機碼。
- **初始化預設租用戶資料庫中的租用戶**： 租用戶資料庫會更新為加入新的租用戶資訊。  
- **在目錄中登錄租用戶**： 新的租用戶金鑰與現有的 tenants1 資料庫之間的對應加入至目錄。 
- **將租用戶的名稱加入目錄延伸模組資料表**： 地點名稱加入至租用戶中的資料表類別目錄。  這個加法示範如何擴充之類別目錄資料庫，以支援其他的應用程式特定資料。
- **開啟新的租用戶的事件頁面**: *Bushwillow 則藍色*事件頁面會在瀏覽器中開啟。

   ![活動](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>偵錯工具的步驟

若要了解 Wingtip 應用程式如何實作新的租用戶佈建共用資料庫中，加入中斷點和逐步執行工作流程：

1. 在 *PowerShell ISE* 中，開啟 ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* 並設定下列參數：
   - **$TenantName** = **Bushwillow Blues**，新場地的名稱。
   - **$VenueType** = **則藍色**，其中一個預先定義的法院類型： 則藍色 classicalmusic、 舞、 jazz、 judo、 motorracing 用途，opera、 rockmusic，足球 （大小寫、 不含空格）。
   - **$DemoScenario** = **1**、 佈建共用的資料庫與其他租用戶的租用戶。

2. 將游標置於行 38，該處會指示行上的任何位置加入中斷點：*新增租用戶 '*，然後按下**F9**。

   ![中斷點](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. 執行指令碼，請按**F5**。

4. 在指令碼於中斷點停止執行之後，請按 **F11** 進入程式碼。

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. 使用 [偵錯] 功能表選項、**F10** 和 **F11** 追蹤指令碼的執行，可以跳過或進入呼叫的函式。

如需對 PowerShell 指令碼進行偵錯的詳細資訊，請參閱[使用 PowerShell 指令碼及對其進行偵錯的祕訣](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise) \(英文\)。

## <a name="provision-a-tenant-in-its-own-database"></a>中的租用戶佈建其*自己*資料庫

#### <a name="major-actions-of-provisioning"></a>主要動作的佈建

以下是您在追蹤指令碼時，逐步執行工作流程的重要元素：

- **計算新的租用戶金鑰**： 雜湊函式用於從租用戶的名稱建立租用戶金鑰。
- **檢查租用戶金鑰已經存在**： 類別目錄進行檢查以確保已經尚未登錄機碼。
- **建立新的租用戶資料庫**： 資料庫由複製*basetenantdb*資料庫使用資源管理員範本。  新的資料庫名稱是以租用戶的名稱作為基礎。
- **將資料庫加入至目錄**： 新的租用戶資料庫會註冊為分區目錄中。
- **初始化預設租用戶資料庫中的租用戶**： 租用戶資料庫會更新為加入新的租用戶資訊。  
- **在目錄中登錄租用戶**： 新的租用戶金鑰之間的對應和*sequoiasoccer*資料庫加入至目錄。
- **租用戶名稱加入至目錄**： 地點名稱加入至租用戶擴充功能中的資料表類別目錄。
- **開啟新的租用戶的事件頁面**: *Sequoia 足球*事件頁面會在瀏覽器中開啟。

   ![活動](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>偵錯工具的步驟

在它自己的資料庫中建立租用戶時，現在引導完成指令碼處理序：

1. 仍在 ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* 中，設定下列參數：
   - **$TenantName** = **Sequoia Soccer**，新場地的名稱。
   - **$VenueType** = **soccer**，其中一個預先定義的場地類型：blues、classicalmusic、dance、jazz、judo、motorracing、multipurpose、opera、rockmusic、soccer (小寫、不含空格)。
   - **$DemoScenario** = **2**、 佈建到它自己的資料庫的租用戶。

2. 將游標置於以第 57 行 (該行顯示︰&&nbsp;$PSScriptRoot\New-TenantAndDatabase `) 上的任意位置來新增中斷點，然後按 **F9**。

   ![中斷點](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. 執行指令碼，請按**F5**。

4. 在指令碼於中斷點停止執行之後，請按 **F11** 進入程式碼。  使用 **F10** 和 **F11** 可以跳過及進入函式追蹤執行。

## <a name="provision-a-batch-of-tenants"></a>佈建一批租用戶

此練習會佈建一批 17 個租用戶。 建議您在開始其他 Wingtip Tickets 教學課程之前佈建這一批租用戶，才會有較多資料庫可以使用。

1. 在 PowerShell ISE 中，開啟 ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*，並將 $DemoScenario 參數變更為 4：
   - **$DemoScenario** = **4**、 佈建到共用資料庫的租用戶的批次。

2. 按 **F5** 並執行指令碼。

### <a name="verify-the-deployed-set-of-tenants"></a>驗證租用戶的已部署集合 

在這個階段，您有混合部署到共用資料庫的租用戶和租用戶部署到他們自己的資料庫。 Azure 入口網站可以用來檢查所建立的資料庫。 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 SQL 伺服器的清單以開啟 **tenants1-mt-\<USER\>** 伺服器。  **SQL 資料庫**清單應該包括共用的 **tenants1** 資料庫，以及位於其自己資料庫中的租用戶之資料庫：

   ![資料庫清單](media/saas-multitenantdb-provision-and-catalog/Databases.png)

儘管 Azure 入口網站會顯示租用戶資料庫，但它不會讓您查看共用資料庫內的租用戶。 中的租用戶的完整清單，可以看到**事件中樞**網頁 Wingtip，並瀏覽目錄。

#### <a name="using-wingtip-tickets-events-hub-page"></a>使用 Wingtip 票證事件中樞 頁面

在瀏覽器中開啟 [事件中樞] 頁面 (http:events.wingtip-mt.\<USER\>.trafficmanager.net)  

#### <a name="using-catalog-database"></a>使用 類別目錄資料庫

使用目錄租用戶和每個對應的資料庫的完整清單。 SQL 檢視的是，提供聯結之租用戶名稱，使其資料庫名稱。 檢視運用示範擴充儲存在目錄中繼資料的值。
- 使用 tenantcatalog 資料庫中的 SQL 檢視。
- 租用戶名稱會儲存在租用戶資料表。
- 資料庫名稱會儲存在分區管理資料表中。

1. 在 SQL Server Management Studio (SSMS)，在連接到租用戶伺服器**目錄機動\<使用者\>.database.windows.net**，登入 =**開發人員**，和密碼 =**P@ssword1**

    ![SSMS 連線對話方塊](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. 在 SSMS 物件總管 中，瀏覽至檢視中的*tenantcatalog*資料庫。

3. 以滑鼠右鍵按一下 *TenantsExtended* 檢視並選擇 [選取前 1000 個資料列]。 請注意租用戶名稱與不同租用戶的資料庫之間的對應。

    ![SSMS 中的 ExtendedTenants 檢視](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>其他佈建模式

本章節將討論其他有趣的佈建模式。

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>彈性集區中預先佈建資料庫

預先佈建模式會利用在使用彈性集區時，針對集區而非資料庫計費這個事實。 因此，可以在需要資料庫而未產生額外成本之前，先將資料庫新增至彈性集區。 這個預先 visioning 大幅減少將佈建至資料庫的租用戶所花費的時間。 可以視需要調整預先佈建的資料庫數目，以針對預期的佈建率保留適當的緩衝。

#### <a name="auto-provisioning"></a>自動佈建

在自動佈建模式中，佈建伺服器、 集區，與資料庫會自動視需要用於專屬的佈建服務。 這項自動化，包括預先佈建彈性集區中的資料庫。 如果資料庫會解除委任，並刪除，這會建立彈性集區中的間距可以填滿視需要佈建的服務。

這種類型的自動化服務可能是簡單或很複雜。 例如，自動化處理跨多個地理位置，佈建，而無法設定災害復原的地理複寫。 具有自動佈建模式中，用戶端應用程式或指令碼會佈建將要求提交到佇列佈建服務所處理。 指令碼，然後會輪詢偵測完成。 如果使用預先佈建時，會處理要求快速，而背景服務管理佈建的取代資料庫。

## <a name="additional-resources"></a>其他資源

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)
- [如何在 Windows PowerShell ISE 中針對指令碼進行偵錯](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 將單一新租用戶佈建到共用的多租用戶資料庫和其自己的資料庫
> * 佈建一批額外的租用戶
> * 逐步了解佈建租用戶和將它們註冊到目錄的細節

試用[效能監視教學課程](saas-multitenantdb-performance-monitoring.md)。

