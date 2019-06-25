---
title: 保護 Azure 資訊安全中心中的 Azure 資料和儲存體服務 |Microsoft Docs
description: 本文件說明「Azure 資訊安全中心」中的建議，這些建議可協助您保護您的資料和 Azure SQL 服務，以及遵守安全性原則。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: monhaber
ms.openlocfilehash: 2ac0e4ebaafb8b0c9c79e885cecbefc5a65c1823
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275282"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>保護 Azure 資訊安全中心中的 Azure 資料和儲存體服務
本主題說明如何檢視和實作資料和儲存體資源的安全性建議。 分析您的 Azure 資源的安全性狀態時，azure 資訊安全中心找到這些建議。

## <a name="view-your-data-security-information"></a>檢視您資料的安全性資訊

1. 在 **資源的安全性防護**區段中，按一下**資料和儲存體資源**。

   ![資料和儲存體資源](./media/security-center-monitoring/click-data.png)

    **資料安全性** 頁面隨即開啟與資料資源的建議。

     ![資料資源](./media/security-center-monitoring/sql-overview.png)

從這個頁面上，您可以：

* 按一下 **概觀**索引標籤會列出要修復的所有資料資源建議。 
* 按一下每個索引標籤，然後檢視依資源類型的建議。

    > [!NOTE]
    > 如需儲存體加密的詳細資訊，請閱讀[在 Azure 資訊安全中心啟用 Azure 儲存體帳戶的加密](security-center-enable-encryption-for-storage-account.md)。


## <a name="remediate-a-recommendation-on-a-data-resource"></a>修復資料資源的建議

1. 從任何 [資源] 索引標籤中，按一下 [資源]。 [資訊] 頁面隨即開啟，列出的建議，以進行補救。

    ![資源資訊](./media/security-center-monitoring/sql-recommendations.png)

2. 按一下 [建議]。 [建議] 頁面隨即開啟並顯示**補救步驟**來實作建議。

   ![補救步驟](./media/security-center-monitoring/remediate1.png)

3. 按一下 **採取**。 資源的 [設定] 頁面隨即出現。

    ![啟用建議](./media/security-center-monitoring/remediate2.png)

4. 請遵循**補救步驟**然後按一下**儲存**。

## <a name="data-and-storage-recommendations"></a>資料和儲存體建議

|資源類型|安全分數|建議|描述|
|----|----|----|----|
|儲存體帳戶|20|應該啟用安全傳輸至儲存體帳戶|安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 HTTPS 可確保伺服器和服務之間的驗證，並保護傳輸中的資料免於網路層攻擊，例如-攔截，竊聽，和工作階段攔截。|
|Redis|20|應該啟用只安全連線至 Redis 快取|只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持。|
|SQL|15|應該啟用 SQL database 上的透明資料加密|啟用透明資料加密，以保護待用資料及符合合規要求。|
|SQL|15|應該啟用 SQL server 稽核|啟用 Azure SQL Server 的稽核。 (僅限 Azure SQL 服務。 不包含在虛擬機器上執行的 SQ。)|
|Data Lake 分析|5|應該啟用 Data Lake Analytics 中的診斷記錄檔|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|Data Lake Store|5|應該啟用 Azure Data Lake Store 中的診斷記錄檔|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|SQL|30|應該修復您的 SQL database 中的弱點|SQL 弱點評量掃描您資料庫的安全性弱點，並會公開任何偏離最佳做法，例如錯誤設定、 過多的權限，以及未受保護的機密資料。 解決找到的弱點可以大幅改進資料庫的安全性等級。|
|SQL|20|為 SQL Server 佈建 Azure AD 系統管理員|為 SQL Server 佈建 Azure AD 系統管理員，以啟用 Azure AD 驗證。 Azure AD 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別。|
|儲存體帳戶|15|具有防火牆和虛擬網路組態的儲存體帳戶的存取權應該限制|稽核儲存體帳戶防火牆設定中不受限制的網路存取。 請改為設定網路規則，只允許來自認可之網路的應用程式存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，您可以授與存取流量來自特定的 Azure 虛擬網路或公用網際網路 IP 位址範圍。|
|儲存體帳戶|1|儲存體帳戶應該移轉至新的 Azure Resource Manager 資源|使用新的 Azure Resource Manager v2 儲存體帳戶提供安全性增強功能，例如： 更強的存取控制 (RBAC)、 較佳的稽核、 Resource Manager 為基礎的部署和管理存取權管理的身分識別，金鑰保存庫的存取祕密，Azure AD 為基礎的驗證及支援的標籤和資源群組，以簡化安全性管理。|

## <a name="see-also"></a>請參閱
若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列主題：

* [保護 Azure 資訊安全中心內的虛擬機器](security-center-virtual-machine-recommendations.md)
* [保護 Azure 資訊安全中心內的應用程式](security-center-application-recommendations.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)

若要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
