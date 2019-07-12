---
title: 威脅偵測 Azure 資訊安全中心中的資料服務 |Microsoft Docs
description: 本主題提供 Azure 資訊安全中心可用的資料服務警示。
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 87cfd2769e473d26c2dcae1b7b418f6fb1739915
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626279"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure 資訊安全中心中的資料服務的威脅偵測

 資訊安全中心會分析資料儲存體服務的記錄檔，並觸發警示，當它偵測到您的資料資源的威脅。 本主題列出之警示的資訊安全中心會產生下列服務：

* [Azure SQL Database 和 SQL 資料倉儲](#data-sql)
* [Azure 儲存體](#azure-storage)

## Azure SQL Database 和 SQL 資料倉儲 <a name="data-sql"></a>

SQL 威脅偵測會偵測異常的活動，指出不尋常且有危害意圖存取或攻擊資料庫。 資訊安全中心會分析 SQL 稽核記錄檔，並執行原生 SQL 引擎中。

|警示|描述|
|---|---|
|**SQL 插入式攻擊的弱點可能會**|應用程式已在資料庫中產生錯誤的 SQL 陳述式。 這可能表示 SQL 插入式攻擊的可能弱點。 錯誤的陳述式之所以產生，有兩項可能的原因：在應用程式程式碼的缺失建構錯誤的 SQL 陳述式。 或者，應用程式程式碼或預存程序未處理使用者輸入建構錯誤的 SQL 陳述式，這可能會遭到 SQL 插入式攻擊時。|
|**潛在的 SQL 插入式攻擊**|針對已識別的應用程式容易遭受 SQL 插入式攻擊發生作用中的攻擊。 這表示攻擊者嘗試插入惡意使用有弱點的應用程式程式碼的 SQL 陳述式或預存程序。|
|**從不尋常的位置存取**|已變更為 SQL server，其中有人登入到 SQL server 從不尋常的地理位置的存取模式。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。|
|**從不熟悉的主體的存取**|已在 SQL server 的存取模式中變更-有人使用不尋常的主體 （SQL 使用者） 的 SQL server 登入。 在某些情況下，警示會偵測到合法的動作 (新的應用程式、開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。|
|**從可能有害的應用程式的存取**|已使用可能有害的應用程式來存取資料庫。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測使用常見攻擊工具的攻擊。|
|**暴力 SQL 認證**|發生異常大量使用不同的認證登入失敗。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測暴力攻擊。|

如需有關 SQL 威脅偵測警示，請參閱 <<c0> [ Azure SQL Database 威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)，並檢閱 [威脅偵測警示] 區段。 另請參閱[如何 Azure 資訊安全中心可協助顯示網路](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/)若要檢視資訊安全中心來探索攻擊所使用的惡意 SQL 活動偵測的範例。

## Azure Storage<a name="azure-storage"></a>

>[!NOTE]
> Azure 儲存體的進階的威脅防護只是目前適用於 Blob 儲存體。 

Azure 儲存體的進階威脅防護可多提供一道安全智慧，偵測儲存體帳戶中異常而且可能有害的存取或攻擊意圖。 此圖層的保護可讓您解決威脅，而不需要您是安全性專家，以及管理安全性監視系統。

資訊安全中心會分析診斷記錄的讀取、 寫入及刪除 Blob 儲存體中的要求來偵測威脅，並在活動中的異常狀況發生時，它會觸發警示。 如需詳細資訊，請參閱[設定儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)如需詳細資訊。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**不尋常的位置存取異常**|取樣的網路流量分析偵測到源自於您的部署中的資源的異常連出遠端桌面通訊協定 (RDP) 通訊。 此活動會被視為異常情況，此環境，且可能表示您的資源已遭入侵，且現在用於對外部 RDP 端點暴力密碼破解。 請注意，這類型的活動可能會導致外部實體將您的 IP 標記為惡意。|
|**應用程式存取異常**|表示不尋常的應用程式具有存取此儲存體帳戶。 可能的原因是，攻擊者存取您使用新的應用程式的儲存體帳戶。|
|**匿名存取異常**|表示儲存體帳戶的存取模式中的變更。 比方說，此帳戶已存取以匿名方式 （不需要驗證），這是預期相較於最新的存取模式，此帳戶。 可能的原因是，攻擊者程式碼利用到保留個儲存體容器的公用讀取權限。|
|**資料外洩異常**|表示已擷取相較於此儲存體容器上的最近活動的異常大量的資料。 可能的原因是攻擊者已經擷取大量的資料，從保留個儲存體容器。|
|**意外的刪除異常**|表示一或多個未預期的刪除作業發生在儲存體帳戶中，相較於最近的活動，此帳戶。 可能的原因是，攻擊者已刪除的資料，從儲存體帳戶。|
|**Azure 雲端服務套件上傳**|指出，Azure 雲端服務封裝 （.cspkg 檔） 已上傳至儲存體帳戶以不尋常的方式，相較於最近的活動，此帳戶。 可能的原因是，攻擊者有已準備要部署至 Azure 雲端服務在從儲存體帳戶的惡意程式碼。|
|**權限存取異常**|表示此儲存體容器的存取權限，以不尋常的方式有所變更。 可能的原因是，攻擊者已變更容器權限來降低其安全性狀態，或取得持續性。|
|**檢查存取異常**|表示儲存體帳戶的存取權限都已經檢查過不尋常的方式，相較於最近的活動，此帳戶。 可能的原因是攻擊者已執行未來遭受攻擊的探查。|
|**資料探索異常**|指出 blob 或容器儲存體帳戶中的都已列舉以異常方式，相較於最近的活動，此帳戶。 可能的原因是攻擊者已執行未來遭受攻擊的探查。|

>[!NOTE]
>進階的威脅防護，Azure 儲存體目前不是適用於 Azure government 和主權雲端區域。

如需有關儲存體的警示的詳細資訊，請參閱[Azure 儲存體的進階威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)一文，並檢閱 [保護警示] 區段。
