---
title: Azure 資訊安全中心中的資料服務威脅偵測 |Microsoft Docs
description: 本主題提供 Azure 資訊安全中心中可用的資料服務警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: memildin
ms.openlocfilehash: 7e45dffd15cdf2acf15c7d46ed0cf42fc5997a6a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244567"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure 資訊安全中心中的資料服務威脅偵測

 Azure 資訊安全中心會分析資料儲存體服務的記錄，並在偵測到資料資源的威脅時觸發警示。 本主題列出資訊安全中心針對下列服務產生的警示：

* [Azure SQL Database 和 Azure SQL Data Warehouse](#data-sql)
* [Azure 儲存體](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database 和 SQL 資料倉儲<a name="data-sql"></a>

SQL 威脅偵測會識別異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。 

|警示|描述|
|---|---|
|**SQL 插入的弱點**|應用程式在資料庫中產生了錯誤的 SQL 語句。 這可能表示 SQL 插入式攻擊可能有弱點。 有兩個可能的原因會造成錯誤的語句。 應用程式程式碼中的瑕疵可能已建立了錯誤的 SQL 語句。 或者，在建立錯誤的 SQL 語句時，應用程式代碼或預存程式不會淨化使用者輸入，這可能會被用於 SQL 插入式攻擊。|
|**潛在的 SQL 插入式攻擊**|已識別的應用程式對 SQL 插入式攻擊所發生的主動入侵。 這表示攻擊者正嘗試使用易受攻擊的應用程式代碼或預存程式來插入惡意的 SQL 語句。|
|**從不尋常的位置存取**|SQL Server 的存取模式有所變更，其中有人從不尋常的地理位置登入伺服器。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則會偵測惡意動作（先前的員工或外部攻擊者）。|
|**從不熟悉的主體存取**|SQL Server 的存取模式有所變更。 有人使用不尋常的主體（使用者）登入伺服器。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則會偵測惡意動作（先前的員工或外部攻擊者）。|
|**從可能有害的應用程式存取**|可能有害的應用程式已被用來存取資料庫。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示會偵測使用常用工具的攻擊。|
|**暴力密碼破解 SQL 認證**|發生異常大量的失敗登入，但有不同的認證。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測暴力攻擊。|

如需有關 SQL 威脅偵測警示的詳細資訊，請參閱[Azure SQL Database 威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)。 特別是，請參閱威脅偵測警示一節。 另請參閱[Azure 資訊安全中心如何協助展現網路攻擊](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/)，以查看資訊安全中心如何使用惡意 SQL 活動偵測來探索攻擊的範例。

## 容量<a name="azure-storage"></a>

>[!NOTE]
> 儲存體的先進威脅防護目前僅適用于 Blob 儲存體。

儲存體的先進威脅防護提供一層額外的安全性情報，可偵測到不尋常且可能有害的存取或惡意探索儲存體帳戶嘗試。 這一層保護可讓您解決威脅，而不需要您成為安全性專家，以及管理安全性監視系統。

資訊安全中心會分析對 Blob 儲存體之讀取、寫入及刪除要求的診斷記錄，以偵測威脅，並在發生異常狀況時觸發警示。 如需詳細資訊，請參閱[設定儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**不尋常的位置存取異常**|取樣網路流量分析偵測到來自您部署中資源的異常傳出遠端桌面通訊協定（RDP）通訊。 此環境的此活動被視為異常。 這可能表示您的資源已遭入侵，且現在用來對外部 RDP 端點進行暴力密碼破解攻擊。 請注意，這種類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**應用程式存取異常**|指出不尋常的應用程式已存取此儲存體帳戶。 可能的原因是攻擊者使用新的應用程式存取了您的儲存體帳戶。|
|**匿名存取異常**|表示儲存體帳戶的存取模式有所變更。 比方說，此帳戶已匿名存取（不含任何驗證），這與此帳戶最近的存取模式是未預期的。 可能的原因是攻擊者已利用對持有 blob 儲存體之容器的公用讀取權限。|
|**Tor 異常**|表示已成功從稱為作用中結束節點 Tor （匿名 proxy）的 IP 位址存取此帳戶。 此警示的嚴重性會考慮所使用的驗證類型（如果有的話），以及這是否為這類存取的第一個案例。 可能的原因可能是使用 Tor 存取您儲存體帳戶的攻擊者，或是已使用 Tor 存取您儲存體帳戶的合法使用者。|
|**資料外泄異常**|表示相較于此儲存體容器上最近的活動，已解壓縮的資料量異常龐大。 可能的原因是攻擊者已從存放 blob 儲存體的容器中解壓縮大量資料。|
|**非預期的刪除異常**|表示在儲存體帳戶中發生一或多個未預期的刪除作業，相較于此帳戶的最近活動。 可能的原因是攻擊者已從您的儲存體帳戶刪除資料。|
|**上傳 Azure 雲端服務套件**|表示 Azure 雲端服務套件（.cspkg 檔案）已上傳至儲存體帳戶，這與此帳戶上最近的活動相較之下的情況不尋常。 可能的原因是攻擊者已準備好將惡意程式碼從您的儲存體帳戶部署到 Azure 雲端服務。|
|**許可權存取異常**|指出此儲存體容器的存取權限已以不尋常的方式變更。 可能的原因是攻擊者已變更容器許可權，以降低其安全性狀態或取得持續性。|
|**檢查存取異常**|表示已使用不尋常的方式檢查儲存體帳戶的存取權限，相較于此帳戶的最近活動。 可能的原因是攻擊者已針對未來的攻擊執行偵察。|
|**資料探索異常**|表示儲存體帳戶中的 blob 或容器已以異常方式列舉，相較于此帳戶的最近活動。 可能的原因是攻擊者已針對未來的攻擊執行偵察。|

>[!NOTE]
>適用于儲存體的先進威脅防護目前無法在 Azure 政府和主權雲端區域中使用。

如需存放裝置警示的詳細資訊，請參閱[Azure 儲存體的 Advanced 威脅防護](../storage/common/storage-advanced-threat-protection.md)。 請特別參閱「保護警示」一節。

## Azure Cosmos DB<a name="cosmos-db"></a>

下列警示是由不尋常且可能有害的嘗試存取或惡意探索 Azure Cosmos DB 帳戶所產生：

|警示|描述|
|---|---|
|**從不尋常的位置存取**|表示 Azure Cosmos DB 帳戶的存取模式有所變更。 相較于最近的活動，有人從不熟悉的 IP 位址存取了此帳戶。 可能是攻擊者存取了帳戶，或合法的使用者從新的和不尋常的地理位置存取了它。 後者的範例是從新的應用程式或開發人員進行遠端維護。|
|**不尋常的資料外泄**|表示 Azure Cosmos DB 帳戶中的資料提取模式有變更。 相較于最近的活動，有人已解壓縮了不尋常的資料量。 攻擊者可能已從 Azure Cosmos DB 資料庫（例如資料外泄或洩漏，或未經授權的資料傳輸）解壓縮大量資料。 或者，合法的使用者或應用程式可能已從容器中解壓縮了不尋常的資料量（例如，用於維護備份活動）。|

如需詳細資訊，請參閱[Azure Cosmos DB 的 Advanced 威脅防護](../cosmos-db/cosmos-db-advanced-threat-protection.md)。
