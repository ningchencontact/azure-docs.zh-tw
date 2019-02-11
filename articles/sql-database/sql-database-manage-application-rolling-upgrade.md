---
title: 輪流應用程式升級 - Azure SQL Database | Microsoft Docs
description: 了解如何使用 Azure SQL Database 異地複寫以支援雲端應用程式的線上升級。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/29/2019
ms.openlocfilehash: 50f6f114a4d90f48218f751e1649e8694e664491
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295741"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>使用 SQL Database 主動式異地複寫管理雲端應用程式的輪流升級

了解如何使用 SQL Database 中的[主動式異地複寫](sql-database-auto-failover-group.md)來啟用雲端應用程式的輪流升級。 因為升級是干擾性作業，它應該是您商務持續性規劃與設計的一部分。 在本文中，我們將探討兩種不同的升級程序協調方法，並討論每個選項的優點和取捨。 基於本文的目的，我們將使用一個應用程式，它是由連線到單一資料庫作為其資料層的網站所組成。 我們的目標是將應用程式的版本 1 升級到版本 2，而不對使用者體驗造成顯著的影響。

評估升級選項時，您應該考慮下列因素：

* 升級期間對應用程式可用性的影響。 應用程式功能可能受到限制或降級多久時間。
* 升級失敗時的復原能力。
* 升級期間發生不相關的重大失敗時，應用程式的弱點。
* 總金額成本。  這包括額外備援以及升級程序所使用之暫時性元件的累加成本。

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>升級依賴資料庫備份以進行災害復原的應用程式

如果您的應用程式依賴自動資料庫備份，並針對災害復原使用異地復原，則會部署到單一 Azure 區域。 為了將使用者干擾降到最低，您會在該區域中建立預備環境，該環境具有升級相關的所有應用程式元件。 下圖說明升級程序之前的作業環境。 端點 `contoso.azurewebsites.net` 代表 Web 應用程式的生產位置。 若要啟用復原升級的功能，您必須建立含有資料庫完整同步複本的預備位置。 下列步驟將建立升級的預備環境：

1. 在相同區域中建立次要資料庫。 監視次要資料庫，查看植入程序是否完成 (1)。
2. 針對名為 'Staging' 的 Web 應用程式，建立新的部署位置。 它會在 DNS 中登錄，其 URL 為 `contoso-staging.azurewebsites.net` (2)。

> [!NOTE]
> 請注意，準備步驟不會影響生產位置，且它可以在完整存取模式下運作。
>  

![SQL Database「異地複寫」組態。 雲端災害復原。](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

一旦準備步驟完成後，應用程式就已準備好實際升級。 下圖說明升級程序相關的步驟。

1. 將主要資料庫設為唯讀模式 (3)。 這個模式會保證 Web 應用程式的生產位置 (V1) 在升級期間會保持唯讀狀態，藉此防止 V1 與 V2 資料庫執行個體間的資料分歧。  
2. 使用規劃的終止模式 (4) 中斷次要資料庫的連線。 它會建立完整同步的主要資料庫獨立複本。 此資料庫將會升級。
3. 將主要資料庫切換為讀寫模式，然後執行升級指令碼 (5)。

![SQL Database 異地複寫組態。 雲端災害復原。](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

如果升級順利完成，您現在已準備好將使用者切換至升級後的應用程式複本。 它現在會成為生產位置。  切換涉及一些步驟，如下圖所示。

1. 啟用 Web 應用程式的生產與預備位置之間的交換作業 (6)。 它會切換兩個位置的 URL。 現在 `contoso.azurewebsites.net` 會指向網站和資料庫 (生產環境) 的 V2 版本。  
2. 如果您不再需要 V1 版本 (其變成交換後的暫存複本)，您可以解除委任預備環境 (7)。

![SQL Database 異地複寫組態。 雲端災害復原。](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

如果升級程序失敗 (例如升級指令碼中發生錯誤)，可能要將預備位置視為遭到危害。 若要將應用程式復原到升級前狀態，您可將生產位置中的應用程式還原至完整存取。 下圖顯示相關的步驟。

1. 將資料庫複本設定為讀寫模式 (8)。 這會還原生產複本的完整 V1 功能。
2. 執行根本原因分析並解除委任預備環境 (9)。

此時應用程式已經可以完全運作，並且可以重複升級步驟。

> [!NOTE]
> 復原不需要 DNS 變更，因為您尚未執行交換作業。

![SQL Database 異地複寫組態。 雲端災害復原。](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

這個選項的重要**優點**是您可以使用一組簡單步驟升級單一區域中的應用程式。 升級的金額成本相對較低。 主要的**取捨**是如果在升級期間發生嚴重失敗，復原到升級前狀態會需要將不同區域的應用程式重新部署，並使用異地復原從備份還原資料庫。 此程序將產生顯著的停機時間。

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>升級依賴資料庫異地複寫來進行災害復原的應用程式

如果您的應用程式主動異地複寫或容錯移轉群組來提供商務持續性，則它至少會部署到兩個不同區域，作用中主要資料庫在主要區域中，而唯讀次要資料庫則在備份區域中。 除了先前提到的因素之外，升級程序也必須保證：

* 升級程序期間，應用程式隨時受到保護以防嚴重失敗。
* 應用程式的異地備援元件會與作用中元件以平行方式升級

若要達成這些目標，除了使用 Web 應用程式部署位置，您還要利用 Azure 流量管理員 (ATM)，其使用含有一個作用中端點和一個備份端點的容錯移轉設定檔。  下圖說明升級程序之前的作業環境。 `contoso-1.azurewebsites.net` 和 `contoso-dr.azurewebsites.net` 網站代表具有完整異地備援的應用程式生產環境。 此生產環境包括下列元件：

1. 主要區域中 Web 應用程式 `contoso-1.azurewebsites.net` 的生產位置 (1)
2. 主要區域中的主要資料庫 (2) 
3. 備份區域中 Web 應用程式的待命執行個體 (3)
4. 備份區域中異地複寫的次要資料庫 (4)
5. 具有線上端點 `contoso-1.azurewebsites.net` 和離線端點 `contoso-dr.azurewebsites.net` 的 Azure 流量管理員效能設定檔

若要啟用復原升級的功能，您必須建立含有應用程式完整同步複本的預備環境。 因為您必須確保應用程式可以快速復原，以防在升級程序期間發生嚴重失敗，因此預備環境也需要異地備援。 建立升級的預備環境需要執行下列步驟：

1. 在主要區域中部署 Web 應用程式的預備位置 (6)
2. 在主要區域中建立次要資料庫 (7)。 設定 Web 應用程式的預備位置以連線到該位置。 
3. 複寫主要區域中的次要資料庫 (這裡稱為「鏈結異地複寫」)，在備份區域中建立另一個異地備援次要資料庫 (8)。
3. 在備份區域中部署 Web 應用程式執行個體的預備位置 (9)，並將它設定為連線在步驟 (9) 中建立的異地備援次要資料庫。


> [!NOTE]
> 請注意，準備步驟不會影響生產位置中的應用程式，且它可在讀寫模式下保持完整功能。

![SQL Database 異地複寫組態。 雲端災害復原。](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

準備步驟完成後，預備環境就準備好升級。 下圖說明升級步驟。

1. 將生產位置中的主要資料庫設定為唯讀模式 (10)。 這個模式會保證生產資料庫 (V1) 在升級期間不會變更，藉此防止 V1 與 V2 資料庫執行個體間的資料分歧。  
2. 使用規劃的終止模式中斷相同區域內次要資料庫的連線 (11)。 它會建立獨立但完全同步的生產資料庫複本。 此資料庫將會升級。
3. 對 `contoso-1-staging.azurewebsites.net`、`contoso-dr-staging.azurewebsites.net` 和預備主要資料庫執行升級指令碼 (12)。 資料庫變更會自動複寫至預備次要資料庫 

![SQL Database 異地複寫組態。 雲端災害復原。](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

如果升級順利完成，您現在已準備好將使用者切換至應用程式的 V2 版本。 下圖說明相關步驟。

1. 在主要區域 (13) 和備份區域 (14) 中啟用 Web 應用程式的生產與預備位置之間的交換作業。 應用程式的 V2 現在成為備份區域中具有備援複本的生產位置。
2. 如果您不再需要 V1 應用程式，您可以解除委任預備環境 (15 和 16)。  

![SQL Database 異地複寫組態。 雲端災害復原。](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

如果升級程序失敗 (例如由於升級指令碼發生錯誤)，可以將預備環境視為不一致狀態。 若要將應用程式復原到升級前狀態，您可還原為在生產環境中使用應用程式的 V1。 下圖顯示必要步驟。

1. 將生產位置中的主要資料庫複本設定為讀寫模式 (17)。 這會還原生產位置中完整的 V1 功能。
2. 執行根本原因分析並修復或移除預備環境 (18 和 19)。

此時應用程式已經可以完全運作，並且可以重複升級步驟。

> [!NOTE]
> 復原不需要 DNS 變更，因為您尚未執行交換作業。

![SQL Database 異地複寫組態。 雲端災害復原。](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

這個選項的重要 **優點** 是您可以以平行方式升級應用程式及其異地備援複本，而不會在升級期間危及商務持續性。 主要的 **取捨** 是它需要每個應用程式元件的雙重備援，因此會產生較高的金額成本。 它也涉及更複雜的工作流程。

## <a name="summary"></a>總結

本文中所描述的兩種升級方法有不同的複雜度與金額成本，但它們都著重於將使用者受限為唯讀作業的時間降到最低。 該時間由升級指令碼的期間直接定義。 時間不會取決於資料庫大小、您選擇的服務層，以及網站設定和其他您無法輕鬆控制的因素。 所有準備步驟會與升級步驟分離，且不會影響生產應用程式。 升級指令的效率是決定升級期間使用者的體驗的重要因素。 因此您改進效率的最佳方式，是把工作焦點放在盡可能提高升級指令碼的效率。  

## <a name="next-steps"></a>後續步驟

* 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)。
* 若要了解 Azure SQL Database 主動異地複寫，請參閱[使用主動式異地複寫建立可讀取的次要資料庫](sql-database-active-geo-replication.md)。
* 若要了解 Azure SQL Database 容錯移轉群組，請參閱[使用自動容錯移轉群組可以啟用多個資料庫透明且協調的容錯移轉](sql-database-auto-failover-group.md)。
* 若要了解 Azure App Service 中的部署位置和預備環境，請參閱[在 Azure App Service 中設定預備環境](../app-service/deploy-staging-slots.md)。  
* 若要了解 Azure 流量管理員設定檔，請參閱[管理 Azure 流量管理員設定檔](../traffic-manager/traffic-manager-manage-profiles.md)。  


