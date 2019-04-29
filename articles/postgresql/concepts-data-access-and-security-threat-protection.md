---
title: 進階威脅防護 - 適用於 PostgreSQL 的 Azure 資料庫
description: 「進階威脅防護」會偵測出暗示對資料庫有潛在安全性威脅的異常資料庫活動。
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 0521283f917d05a56d26503dc6f0af349f1ac1e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560025"
---
# <a name="azure-database-for-postgresql-advanced-threat-protection"></a>適用於 PostgreSQL 的 Azure 資料庫進階威脅防護

適用於 PostgreSQL 的 Azure 資料庫進階威脅防護偵測到異常活動，指出有不尋常及可能有害的活動試圖存取或惡意探索資料庫。

> [!NOTE]
> 進階的威脅防護處於公開預覽狀態。

「威脅防護」是「進階威脅防護」(ATP) 供應項目 (一個整合的進階安全性功能套件) 的一部分。 可存取及管理透過進階的威脅防護[Azure 入口網站](https://portal.azure.com)或使用[REST API](/rest/api/postgresql/serversecurityalertpolicies)。 此功能是適用於一般用途和記憶體最佳化的伺服器。

> [!NOTE]
> 進階威脅防護功能**不**適用於下列 Azure 政府和主權雲端區域：US Gov 德克薩斯州、US Gov 亞利桑那州、US Gov 愛荷華州、US DoD 東部、US DoD 中部、德國中部、德國北部、中國東部、中國東部 2。 如需一般產品可用性，請瀏覽[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。

## <a name="what-is-advanced-threat-protection"></a>什麼是進階威脅防護？

「適用於 PostgreSQL 的 Azure 資料庫」的「進階威脅防護」提供一層新的安全性，藉由提供異常活動的安全性警示，讓客戶能夠在潛在威脅發生時偵測並回應該威脅。 當有可疑的資料庫活動、潛在弱點，以及異常的資料庫存取和查詢模式發生時，使用者就會收到警示。 「適用於 PostgreSQL 的 Azure 資料庫」的「進階威脅防護」將警示與 [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合，除了包括可疑活動的詳細資料之外，也推薦有關如何調查及降低威脅的動作。 「適用於 PostgreSQL 的 Azure 資料庫」的「進階威脅防護」讓您不必是安全性專家，也無須管理進階的安全性監視系統，輕鬆就能解決對資料庫的潛在威脅。 

![進階威脅防護概念](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>進階威脅防護警示 
「適用於 PostgreSQL 的 Azure 資料庫」的「進階威脅防護」會偵測出暗示有不尋常及可能有害之資料庫存取或惡意探索意圖的異常活動，並可觸發下列警示：
- **從不尋常的位置存取**：當有人從不尋常的地理位置登入適用於 PostgreSQL 的 Azure 資料庫伺服器，而使得對適用於 PostgreSQL 的 Azure 資料庫伺服器的存取模式發生變更時，就會觸發此警示。 在某些情況下，警示會偵測到合法的動作 (新的應用程式或開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從不尋常的 Azure 資料中心存取**：當有人從適用於 PostgreSQL 的 Azure 資料庫伺服器上近期發現的不尋常 Azure 資料中心登入該伺服器，而使得對該伺服器的存取模式發生變更時，就會觸發此警示。 在某些情況下，警示會偵測出合法的動作 (您在 Azure、Power BI、「適用於 PostgreSQL 的 Azure 資料庫查詢編輯器」中的新應用程式)。 在其他情況下，警示則是偵測來自 Azure 資源/服務的惡意動作 (離職員工、外部攻擊者)。
- **從不熟悉的主體存取**：當有人使用不尋常的主體 (適用於 PostgreSQL 的 Azure 資料庫使用者) 來登入適用於 PostgreSQL 的 Azure 資料庫伺服器，而使得對該伺服器的存取模式發生變更時，就會觸發此警示。 在某些情況下，警示會偵測到合法的動作 (新的應用程式、開發人員維護)。 在其他情況下，警示則是偵測惡意動作 (離職員工、外部攻擊者)。
- **從可能有害的應用程式存取**：使用可能有害的應用程式用存取資料庫時，會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測使用常見攻擊工具的攻擊。
- **暴力密碼破解適用於 PostgreSQL 的 Azure 資料庫認證**：有使用不同認證的異常大量登入失敗時，會觸發此警示。 在某些情況下，警示會偵測到執行中的滲透測試。 在其他情況下，警示則是偵測暴力攻擊。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
* 如需定價的詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫價格頁面](https://azure.microsoft.com/pricing/details/postgresql/) 
* 使用 Azure 入口網站來設定[適用於 PostgreSQL 的 Azure 資料庫進階威脅防護](howto-database-threat-protection-portal.md)  
