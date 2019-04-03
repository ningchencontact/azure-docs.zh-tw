---
title: 保護 Azure 資訊安全中心內的  Azure SQL 服務和資料 | Microsoft Docs
description: 本文件說明「Azure 資訊安全中心」中的建議，這些建議可協助您保護您的資料和 Azure SQL 服務，以及遵守安全性原則。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 479e6d222c9fc7d007252bab12e807ec337ea9e6
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880775"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>保護 Azure 資訊安全中心內的 Azure SQL 服務和資料
「Azure 資訊安全中心」會分析 Azure 資源的安全性狀態。 當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議。  這些建議適用於下列 Azure 資源類型︰虛擬機器 (VM)、網路、SQL 和資料，以及應用程式。


### <a name="monitor-data-security"></a>監視資料安全性

當您按一下 [防護] 區段中的 [資料安全性] 時，隨即會開啟包含 SQL 和儲存體建議的 [資料資源]。 它也具有資料庫的一般健全狀況狀態 [建議](security-center-sql-service-recommendations.md) 。 如需儲存體加密的詳細資訊，請閱讀[在 Azure 資訊安全中心啟用 Azure 儲存體帳戶的加密](security-center-enable-encryption-for-storage-account.md)。

![資料資源](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

在 [SQL 建議] 底下，您可以按一下任何建議，取得解決問題所需之進一步動作的更多詳細資料。 下列範例顯示已展開的 **SQL Database 上的資料庫稽核和威脅偵測**建議。

![有關 SQL 建議的詳細資料](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

[在 SQL 資料庫上啟用稽核和威脅偵測] 具有下列資訊：

* SQL 資料庫的清單
* 其所在的伺服器
* 此設定是否繼承自伺服器，或此設定在此資料庫中是否為唯一的相關資訊
* 目前的狀態
* 問題的嚴重性

當您在資料庫上按一下以解決這項建議時，便會開啟 [稽核與威脅的偵測]，如下列畫面所示。

![稽核與威脅偵測](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

若要啟用稽核，請選取 [稽核] 選項底下的 [開啟]。

## <a name="data-and-storage-recommendations"></a>資料和儲存體建議

|資源類型|安全分數|建議|描述|
|----|----|----|----|
|儲存體帳戶|20|儲存體帳戶需要安全傳輸|安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持。|
|Redis|20|只允許對您 Azure Cache for Redis 的安全連線|只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持。|
|SQL|15|在 SQL 資料庫上啟用透明資料加密|啟用透明資料加密，以保護待用資料及符合合規要求。|
|SQL|15|在 SQL Server 上啟用稽核|啟用 Azure SQL Server 的稽核。 (僅限 Azure SQL 服務。 不包含在虛擬機器上執行的 SQ。)|
|SQL|15|在 SQL Database 上啟用稽核|啟用 Azure SQL Database 的稽核。 (僅限 Azure SQL 服務。 不包含在虛擬機器上執行的 SQ。)|
|Data Lake 分析|15|啟用 Data Lake Analytics 的待用加密|啟用透明資料加密以保護 Data Lake Analytics 中的待用資料。 待用加密是在背景中自動進行的，也就是說 Data Lake Analytics 會在保存資料前自動將資料加密，並在擷取資料前自動將資料解密。 在與 Data Lake Analytics 互動的應用程式和服務中，無須因為加密而進行任何變更。 待用加密不僅可將實體竊取所造成的資料遺失風險降到最低，還有助於滿足法規合規性需求。|
|Data Lake Store|15|啟用 Data Lake Store 的待用加密|啟用透明資料加密以保護 Data Lake Store 中的待用資料。 待用加密是在背景中自動進行的，也就是說 Data Lake Store 會在保存資料前自動將資料加密，並在擷取資料前自動將資料解密。 您無須在與 Data Lake Store 互動的應用程式和服務中，為配合加密而進行任何變更。 待用加密不僅可將實體竊取所造成的資料遺失風險降到最低，還有助於滿足法規合規性需求。|
|Data Lake 分析|5|啟用 Data Lake Analytics 診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|Data Lake Store|5|啟用 Azure Data Lake Store 的診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|SQL|30|修復 SQL 資料庫的弱點|SQL 弱點評定會掃描資料庫有無安全性弱點，並公開任何與最佳做法不同的差異，例如設定不正確、過多權限，以及未保護敏感性資料等等。 解決找到的弱點可以大幅改進資料庫的安全性等級。|
|SQL|20|為 SQL Server 佈建 Azure AD 系統管理員|為 SQL Server 佈建 Azure AD 系統管理員，以啟用 Azure AD 驗證。 Azure AD 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。|
|儲存體帳戶|15|停用儲存體帳戶不受限制的存取|稽核儲存體帳戶防火牆設定中不受限制的網路存取。 請改為設定網路規則，只允許來自認可之網路的應用程式存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，您可以授與存取流量來自特定的 Azure 虛擬網路或公用網際網路 IP 位址範圍。|
|儲存體帳戶|1|將儲存體帳戶移轉至 Azure Resource Manager 模型的資源|使用新的 Azure Resource Manager v2 儲存體帳戶提供安全性增強功能，例如： 更強的存取控制 (RBAC)、 較佳的稽核、 Resource Manager 為基礎的部署和管理存取權管理的身分識別，金鑰保存庫的存取祕密，Azure AD 為基礎的驗證及支援的標籤和資源群組，以簡化安全性管理。|

## <a name="see-also"></a>請參閱
若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

* [保護 Azure 資訊安全中心內的虛擬機器](security-center-virtual-machine-recommendations.md)
* [保護 Azure 資訊安全中心內的應用程式](security-center-application-recommendations.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)

如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
