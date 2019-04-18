---
title: 加速資料庫復原 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database 有一項新功能，可針對 Azure SQL Database 中的單一與集區資料庫，以及 Azure SQL 資料倉儲中的資料庫，提供快速且一致的資料庫復原、瞬間完成的交易回復，以及主動式記錄截斷。
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: bb88da48f8961969176fd67bf6e5fa346655aeac
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677811"
---
# <a name="accelerated-database-recovery-preview"></a>加速資料庫復原 (預覽)

**加速資料庫復原 (ADR)** 是新的 SQL 資料庫引擎功能，藉由重新設計 SQL 資料庫引擎復原程序，來大幅改善資料庫可用性 (特別是在有長時間執行的交易時)。 ADR 目前適用於 Azure SQL Database 中的單一與集區資料庫，以及 Azure SQL 資料倉儲中的資料庫。 ADR 的主要優點如下：

- **快速且一致的資料庫復原**

  使用 ADR，長時間執行的交易不會影響整體復原時間，不論系統中作用中的交易數目或其大小為何，均會啟用快速且一致的資料庫復原。

- **瞬間完成的交易回復**

  使用 ADR，無論交易處於作用中的時間或已執行的更新數目為何，交易回復都會在瞬間完成。

- **主動式記錄截斷**

  使用 ADR，即使有長時間執行的作用中交易存在，還是會主動截斷交易記錄，以防止它的規模失控。

## <a name="the-current-database-recovery-process"></a>目前的資料庫復原程序

SQL Server 中的資料庫復原會遵循 [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) \(英文\) 復原模式並包含下圖所示的三個階段，並且會在下圖後面進行更詳細的說明。

![目前的復原程序](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **分析階段**

  轉寄掃描的交易記錄檔的起點的最後一個成功的檢查點 （或最舊的中途分頁 LSN） 結束之前，以判斷 SQL Server 已停止的時間在每個交易的狀態。

- **重做階段**

  從最舊未認可的交易結束之前，將資料庫帶到就是在損毀時的重做所有的認可的作業的狀態轉寄掃描的交易記錄檔。

- **復原階段**

  對於每個在發生毀損時處於作用中的交易，向後周遊記錄，以復原此交易所執行的作業。

根據此設計，SQL 資料庫引擎從非預期的重新啟動進行復原所花費的時間，(大約) 會與發生損毀時系統中最長的作用中交易大小成正比。 復原需要回復所有未完成的交易。 所需的時間長度會與已執行交易的工作及其處於作用中的時間成正比。 因此，SQL Server 復原程序會在有長時間執行的交易時花費很長的時間 (例如，大量插入作業，或針對大型資料表進行索引建置作業)。

同時，根據此設計來取消/回復大型交易也會花費很長的時間，因為它使用如前所述的相同復原階段。

此外，SQL 資料庫引擎無法在有長期執行的交易時截斷交易記錄，因為需要針對復原和回復程序使用其對應的記錄檔記錄。 由於這項設計的 SQL database engine，有些客戶會面臨的交易記錄檔的大小變得十分龐大，且會耗用大量磁碟空間的問題。

## <a name="the-accelerated-database-recovery-process"></a>加速資料庫復原程序

ADR 會透過完全重新設計 SQL 資料庫引擎復原程序來解決上述問題：

- 藉由避免從最舊的作用中交易開頭來回掃描記錄，以使其保持固定的時間/瞬間完成。 使用 ADR，交易記錄檔只會處理從最後一個成功的檢查點 （或最舊的中途分頁記錄序號 (LSN)）。 因此，復原時間不會受到長時間執行的交易所影響。
- 將所需的交易記錄空間降至最低，因為不再需要處理整個交易的記錄。 因此，能夠在發生檢查點和備份時主動截斷交易記錄。

概括而言，ADR 會藉由對所有實體資料庫修改進行版本控制且只復原邏輯作業，來達到快速的資料庫復原，而這會受到限制且幾乎可立即復原。 任何在發生毀損時處於作用中的交易都會標記為已中止，因此，並行使用者查詢會忽略這些交易所產生的任何版本。

ADR 復原程序有三個與目前復原程序相同的階段。 下圖說明這三個階段使用 ADR 運作的方式，並且會在下圖後面進行更詳細的說明。

![ADR 復原程序](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **分析階段**

  此程序會維持今天加上重新建構了 sLog 並複製非版本控制作業的記錄檔記錄的相同。
  
- **重做**階段

  分成兩個階段 (P)
  - 階段 1

      從 sLog (最舊的未認可交易直到最後一個檢查點) 重做。 重做是一項快速作業，因為它只需處理來自 sLog 的一些記錄。
      
  - 階段 2

     從交易記錄進行重做，會從最後一個檢查點 (而不是最舊的未認可交易) 開始
     
- **復原階段**

   使用 ADR 的復原階段可透過下列方式以幾乎瞬間的方式來完成：使用 sLog 來復原非版本控制的作業及具備邏輯還原的持續版本存放區 (PV)，來執行以版本為基礎的資料列層級復原。

## <a name="adr-recovery-components"></a>ADR 復原元件

ADR 的四個主要元件如下：

- **持續版本存放區 (PV)**

  持續版本存放區是新的 SQL 資料庫引擎機制，可用來保存在資料庫本身而不是傳統 `tempdb` 版本存放區所產生的資料列版本。 PVS 會啟用資源隔離，以及改善可讀取之次要複本的可用性。

- **邏輯還原**

  邏輯還原是非同步程序負責執行資料列層級版本為基礎的復原-已建立版本的所有作業都提供立即的交易回復和復原。

  - 追蹤所有已中止的交易
  - 針對所有使用者交易，使用 PVS 來執行回復
  - 在交易中止之後，立即釋放所有鎖定

- **sLog**

  sLog 是次要的記憶體中記錄資料流，會針對非版本控制的作業 (例如，中繼資料快取失效、鎖定取得等等) 儲存記錄檔記錄。 sLog 是：

  - 低容量且在記憶體中
  - 藉由在檢查點處理期間進行序列化，保存於磁碟上
  - 當交易認可時定期截斷
  - 藉由只處理非版本控制的本的作業來加速重做和復原  
  - 藉由只保留必要的記錄檔記錄來啟用主動式交易記錄截斷

- **清除工具**

  清除工具是一個非同步程序，可定期喚醒並清除不需要的分頁版本。

## <a name="who-should-consider-accelerated-database-recovery"></a>誰應該考慮啟用加速資料庫復原

下列類型的客戶應該考慮啟用 ADR：

- 具有長時間執行交易之工作負載的客戶。
- 看過下列案例的客戶：作用中的交易會導致交易記錄大幅成長。  
- 因為 SQL Server 長時間執行復原 (例如，未預期地重新啟動 SQL Server 或手動進行交易回復)，而導致其體驗到長時間無法使用資料庫的客戶。

## <a name="to-enable-adr-during-this-preview-period"></a>在此預覽期間啟用 ADR

在此功能的預覽期間，傳送電子郵件到 [adr@microsoft.com](mailto:adr@microsoft.com)，以深入了解並試用加速資料庫復原 (ADR)。 在電子郵件中，包含您 SQL Database 伺服器的名稱 (適用於 SQL Database 中的單一與集區資料庫，以及 Azure SQL 資料倉儲中的資料庫)。 由於這是一項預覽功能，因此，您的測試伺服器應該是非生產環境的伺服器。
