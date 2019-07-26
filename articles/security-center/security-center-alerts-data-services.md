---
title: Azure 資訊安全中心中的資料服務威脅偵測 |Microsoft Docs
description: 本主題提供 Azure 資訊安全中心中可用的資料服務警示。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/24/2019
ms.author: v-mohabe
ms.openlocfilehash: f33b69ac443a1bb8f6b7d6e1b19f2f077bf38f58
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501471"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure 資訊安全中心中的資料服務威脅偵測

 資訊安全中心會分析資料儲存體服務的記錄, 並在偵測到資料資源的威脅時觸發警示。 本主題列出資訊安全中心針對下列服務產生的警示:

* [Azure SQL Database 和 SQL 資料倉儲](#data-sql)
* [Azure 儲存體](#azure-storage)
* [Cosmos DB](#cosmos-db)

## Azure SQL Database 和 SQL 資料倉儲<a name="data-sql"></a>

SQL 威脅偵測會偵測異常活動, 指出有不尋常且可能有害的嘗試存取或惡意探索資料庫。 資訊安全中心會分析 SQL audit 記錄, 並在 SQL 引擎中以原生方式執行。

|警示|描述|
|---|---|
|**SQL 插入的弱點**|應用程式在資料庫中產生了錯誤的 SQL 語句。 這可能表示 SQL 插入式攻擊可能有弱點。 錯誤的陳述式之所以產生，有兩項可能的原因：可能的話, 應用程式程式碼中的缺陷會構成錯誤的 SQL 語句。 或者, 在建立錯誤的 SQL 語句時, 應用程式代碼或預存程式不會淨化使用者輸入, 這可能會用於 SQL 插入式攻擊。|
|**潛在的 SQL 插入式攻擊**|已識別的應用程式對 SQL 插入式攻擊所發生的主動入侵。 這表示攻擊者正嘗試使用易受攻擊的應用程式代碼或預存程式來插入惡意的 SQL 語句。|
|**從不尋常的位置存取**|SQL server 的存取模式有所變更, 其中有人從不尋常的地理位置登入 SQL server。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。|
|**從不熟悉的主體存取**|SQL server 的存取模式有所變更-有人使用不尋常的主體 (SQL 使用者) 登入 SQL server。 在某些情況下，警示會偵測到合法的動作 (新的應用程式、開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。|
|**從可能有害的應用程式存取**|可能有害的應用程式已被用來存取資料庫。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測使用常見攻擊工具的攻擊。|
|**暴力密碼破解 SQL 認證**|發生異常大量具有不同認證的失敗登入。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測暴力攻擊。|

如需有關 SQL 威脅偵測警示的詳細資訊, 請參閱[Azure SQL Database 威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview), 以及查看威脅偵測警示一節。 另請參閱[Azure 資訊安全中心如何協助展現網路攻擊](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/), 以查看資訊安全中心如何使用惡意 SQL 活動偵測來探索攻擊的範例。

## Azure 儲存體<a name="azure-storage"></a>

>[!NOTE]
> Azure 儲存體的先進威脅防護目前僅適用于 Blob 儲存體。

Azure 儲存體的進階威脅防護可多提供一道安全智慧，偵測儲存體帳戶中異常而且可能有害的存取或攻擊意圖。 這一層保護可讓您解決威脅, 而不需要您成為安全性專家, 也不會管理安全性監視系統。

資訊安全中心會分析對 Blob 儲存體之讀取、寫入及刪除要求的診斷記錄, 以偵測威脅, 並在發生異常狀況時觸發警示。 如需詳細資訊, 請參閱[設定儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)以取得詳細資訊。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**不尋常的位置存取異常**|取樣的網路流量分析偵測到來自您部署中資源的異常傳出遠端桌面通訊協定 (RDP) 通訊。 此活動在此環境中被視為異常, 可能表示您的資源已遭入侵, 且現在已用來強制外部 RDP 端點進行暴力密碼破解。 請注意，這類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**應用程式存取異常**|指出不尋常的應用程式已存取此儲存體帳戶。 可能的原因是攻擊者已使用新的應用程式存取您的儲存體帳戶。|
|**匿名存取異常**|表示儲存體帳戶的存取模式有所變更。 比方說, 此帳戶已匿名存取 (不含任何驗證), 這與此帳戶最近的存取模式是未預期的。 可能的原因是攻擊者已利用持有 blob 儲存體之容器的公用讀取權限。|
|**資料外泄異常**|表示相較于此儲存體容器上最近的活動, 已解壓縮的資料量異常龐大。 可能的原因是攻擊者已從保留 blob 儲存體的容器中解壓縮大量資料。|
|**非預期的刪除異常**|表示在儲存體帳戶中發生一或多個未預期的刪除作業, 相較于此帳戶的最近活動。 可能的原因是攻擊者已從您的儲存體帳戶刪除資料。|
|**上傳 Azure 雲端服務套件**|表示 Azure 雲端服務套件 (. .cspkg 檔案) 已上傳至儲存體帳戶, 但與此帳戶上最近的活動相較之下, 不尋常。 可能的原因是攻擊者已準備好將惡意程式碼從您的儲存體帳戶部署到 Azure 雲端服務。|
|**許可權存取異常**|指出此儲存體容器的存取權限已以不尋常的方式變更。 可能的原因是攻擊者已變更容器許可權, 以降低其安全性狀態或取得持續性。|
|**檢查存取異常**|表示已使用不尋常的方式檢查儲存體帳戶的存取權限, 相較于此帳戶的最近活動。 可能的原因是攻擊者已針對未來的攻擊執行偵察。|
|**資料探索異常**|表示儲存體帳戶中的 blob 或容器已以異常方式列舉, 相較于此帳戶的最近活動。 可能的原因是攻擊者已針對未來的攻擊執行偵察。|

>[!NOTE]
>Azure 儲存體的先進威脅防護目前無法在 Azure 政府和主權雲端區域中使用。

如需有關存放裝置警示的詳細資訊, 請參閱[Azure 儲存體的 Advanced 威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)一文, 以及查看保護警示一節。

## Cosmos DB<a name="cosmos-db"></a>

下列警示是由不尋常且可能有害的嘗試存取或惡意探索 Azure Cosmos DB 帳戶所產生:

|警示|描述|
|---|---|
|**從不尋常的位置存取**|表示 Cosmos DB 帳戶的存取模式有所變更。 相較于最近的活動, 有人從不熟悉的 IP 位址存取了此帳戶。 攻擊者已存取 Cosmos DB 帳戶, 或合法的使用者已從新的和不尋常的地理位置存取 Cosmos DB 帳戶。 例如: 從遠端進行的新應用程式或開發人員維護。|
|**不尋常的資料外泄**|表示 Cosmos DB 帳戶中的資料提取模式有變更。 相較于最近的活動, 有人已解壓縮了不尋常的資料量。 攻擊者已從 Cosmos DB 資料庫中解壓縮大量資料。 例如: 資料外泄/洩漏、未經授權的資料傳輸。 或者, 合法的使用者或應用程式已從容器中解壓縮了不尋常的資料量。 例如: 維護備份活動。|

如需詳細資訊, 請參閱[Azure Cosmos DB 的 Advanced 威脅防護](../cosmos-db/cosmos-db-advanced-threat-protection.md)。