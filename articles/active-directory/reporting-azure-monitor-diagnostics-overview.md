---
title: Azure 監視器 (預覽) 中的 Azure Active Directory 活動記錄 | Microsoft Docs
description: Azure 監視器 (預覽) 中的 Azure Active Directory 活動記錄概觀
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240097"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Azure 監視器 (預覽) 中的 Azure Active Directory 活動記錄

現在，您可以使用 Azure 監視器，將 Azure AD 活動記錄路由至您自己的儲存體帳戶或事件中樞。 透過 Azure 監視器中的公開預覽版 Azure Active Directory 記錄，您可以：

* 封存 Azure 儲存體帳戶的稽核記錄，讓您長時間保存資料
* 將稽核記錄串流至 Azure 事件中樞，以使用 Splunk 和 QRadar 等常用的 SIEM 工具進行分析
* 將稽核記錄串流至事件中樞，將其與您自己的自訂記錄解決方案整合

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>支援的報告

您可以使用這項功能將稽核活動記錄和登入活動記錄路由至您的 Azure 儲存體帳戶、事件中樞或自訂解決方案。 

* **稽核記錄**：[稽核記錄活動報告](active-directory-reporting-activity-audit-logs.md)可讓您對每個在租用戶中執行的工作存取歷程記錄。
* **登入**：透過[登入活動報告](active-directory-reporting-activity-sign-ins.md)，您可以判斷是誰執行了稽核記錄報告所報告的工作。

> [!NOTE]
> 目前不支援與 B2C 相關的稽核和登入活動記錄。
>

## <a name="prerequisites"></a>必要條件

若要使用此功能，您必須要有：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以[註冊免費試用](https://azure.microsoft.com/free/)。
* Azure AD Free、Basic、Premium 1 或 Premium 2 [授權](https://azure.microsoft.com/pricing/details/active-directory/)，用以存取 Azure 入口網站中的 Azure AD 記錄。 

根據您要路由稽核記錄資料的位置，您必須要有：

* 您具有 *ListKeys* 權限的 Azure 儲存體帳戶。 建議您使用一般儲存體帳戶，而不要使用 Blob 儲存體帳戶。 如需儲存體的定價資訊，請參閱 [Azure 儲存體定價計算機](https://azure.microsoft.com/pricing/calculator/?service=storage)。 
* Azure 事件中樞命名空間，以便與第三方解決方案整合。

> [!NOTE]
> 您必須是 Azure AD 租用戶中的全域系統管理員或安全性系統管理員，才能存取 Azure AD 活動記錄。
>

## <a name="cost-considerations"></a>成本考量

如果您已有 Azure AD 授權，您必須要有用來設定儲存體帳戶和事件中樞的 Azure 訂用帳戶。 Azure 訂用帳戶會免費提供，但是您必須付費才能使用 Azure 資源，包括用於封存的儲存體帳戶，和用於串流的事件中樞。 資料量和連帶產生的費用會隨著租用戶大小而有大幅差異。 

### <a name="storage-size-for-activity-logs"></a>活動記錄的儲存體大小

每個稽核記錄事件最多會使用約 2KB 的資料儲存體。 因此，以具有 100,000 名使用者的租用戶而言，每天大約會產生 150 萬個事件，因此您每天需要約 3 GB 的資料儲存體。 由於寫入會以約 5 分鐘的批次執行，因此您可以預期每個月大概會有 9000 個寫入作業。 下表包含以租用戶大小為準的約略估計成本，儲存體帳戶是位於美國西部的一般用途 v2 帳戶，保留期間至少一年。 請使用 [Azure 儲存體定價計算機](https://azure.microsoft.com/pricing/details/storage/blobs/)，針對您預期應用程式所將使用的資料量建立更精確的估計值。 每個稽核記錄事件最多會使用約 2KB 的資料儲存體。 因此，以具有 100,000 名使用者的租用戶而言，每天大約會產生 150 萬個事件，因此您每天需要約 3 GB 的資料儲存體。 由於寫入會以約 5 分鐘的批次執行，因此您可以預期每個月大概會有 9000 個寫入作業。 下表包含以租用戶大小為準的約略估計成本，儲存體帳戶是位於美國西部的一般用途 v2 帳戶，保留期間至少一年。 請使用 [Azure 儲存體定價計算機](https://azure.microsoft.com/pricing/details/storage/blobs/)，針對您預期應用程式所將使用的資料量建立更精確的估計值。 

| 記錄分類 | 使用者人數 | 每日事件數目 | 每個月的約略資料量 | 每個月的約略成本 | 每年的約略成本 |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| 稽核 | 100,000 | 150 萬 | 90 GB | $1.93 | $23.12 |
| 稽核 | 1000 | 15000 | 900 MB | $0.02 | $0.24 |
| 登入 | 1000 | 34800 | 4 GB | $0.13 | $1.56 |
| 登入 | 100,000 | 1500 萬 | 1.7 TB | $35.41 | $424.92 | 


### <a name="event-hub-messages-for-activity-logs"></a>活動記錄的事件中樞訊息

事件會以約五分鐘的間隔分批處理，並以包含該時間範圍內所有事件的單一訊息形式傳送。 事件中樞內的訊息大小上限為 256 KB，如果在時間範圍內所有訊息的大小超過該數量，就會傳送多個訊息。 

例如，使用者人數超過 100,000 名的大型租用戶每秒通常會有約 18 個事件，相當於每 5 分鐘有 5400 個事件。 由於每個事件的稽核記錄大約為 2KB，這相當於 10.8 MB 的資料，因此在這 5 分鐘的間隔內，將會有 43 個訊息傳送至事件中樞。 下表包含美國西部的基本事件中樞以事件資料量為準的約略成本。 請使用[事件中樞定價計算機](https://azure.microsoft.com/pricing/details/event-hubs/)，針對您預期應用程式所將使用的資料量計算精確的估計值。

| 記錄分類 | 使用者人數 | 每秒事件數目 | 每 5 分鐘間隔的事件數目 | 每一間隔的數量 | 每一間隔的訊息數目 | 每個月的訊息數目 | 每個月的約略成本 |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| 稽核 | 100,000 | 18 | 5400 | 10.8 MB | 43 | 371,520 | $10.83 |
| 稽核 | 1000 | 0.1 | 52 | 104 KB | 1 | 8640 | $10.8 |
| 登入 | 1000 | 178 | 53400 | 106.8 MB | 418 | 3,611,520 | $11.06 |  


## <a name="frequently-asked-questions"></a>常見問題集

此節包含 Azure 監視器中的 Azure Active Directory 記錄的常見問題集和已知問題。

**問：我該從何處著手？** 

**答：** 請先檢視[概觀](reporting-azure-monitor-diagnostics-overview.md)，以大致了解部署這項功能所需的條件。 在您熟悉先決條件後，請利用教學課程進行設定以及將記錄路由至事件中樞。

---

**問：其中包含哪些記錄？**

**答：** 登入和稽核記錄都可透過這項功能來路由，但目前不包含 B2C 相關稽核事件。 請參閱[稽核記錄結構描述](reporting-azure-monitor-diagnostics-audit-log-schema.md)和[登入記錄結構描述](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)，以了解目前支援哪些類型的記錄和哪些以功能為基礎的記錄。 

---

**問：在執行動作後，多久會在事件中樞內顯示對應的記錄？**

**答：** 在執行動作後，記錄應該會在二到五分鐘內顯示於事件中樞。 如需事件中樞的詳細資訊，請參閱[什麼是事件中樞？](/azure/event-hubs/event-hubs-what-is-event-hubs.md)

---

**問：在執行動作後，多久會在儲存體帳戶中顯示對應的記錄？**

**答：** 就 Azure 儲存體帳戶而言，延遲介於執行動作後的 5 到 15 分鐘之間。

---

**問：儲存資料需要多少成本？**

**答：** 儲存體成本取決於您的記錄大小，以及您所選擇的保留期間。 如需以產生的記錄量為準的租用戶約略成本估計值，請參閱[概觀](reporting-azure-monitor-diagnostics-overview.md)。

---

**問：將資料串留置事件中樞需要多少成本？**

**答：** 串流成本取決於您每分鐘收到的訊息數目。 請參閱[概觀](reporting-azure-monitor-diagnostics-overview.md)以深入了解成本的計算方式，以及尋找以訊息數目為基礎的約略估計值。 

---

**問：目前支援哪些 SIEM 工具？** 

**答：** 目前支援 Azure 監視器的有 Splunk、QRadar 及 Sumologic。 不過，Splunk 是唯一支援 Azure Active Directory 記錄的 SIEM 工具。 如需連接器運作方式的詳細資訊，請參閱[將 Azure 監視資料串流至事件中樞以供外部工具取用](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)。

---

**問：是否可在不使用外部 SIEM 工具的情況下從事件中樞存取資料？** 

**答：** 是的，您可以使用[事件中樞 API](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) 從自訂應用程式存取記錄。 

---


## <a name="next-steps"></a>後續步驟

* [將活動記錄封存至儲存體帳戶](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [將活動記錄路由至事件中樞](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [深入了解 Azure 診斷記錄檔](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)