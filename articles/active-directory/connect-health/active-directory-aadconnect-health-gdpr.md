---
title: "Azure AD Connect Health 和一般資料保護規定 | Microsoft Docs"
description: "本文件說明如何使用 Azure AD Connect 取得 GDPR 合規性。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: d66f717f546271a5e5c3c49d6cbaef1c190d18d8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>GDPR 合規性與 Azure AD Connect Health 

[一般資料保護規定 (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) 是歐盟 (EU) 所制定的資料保護和隱私權法律。 GDPR 會針對想要提供商品和服務給 EU 居民，或想要收集和分析 EU 居民相關資料的公司、政府機關、非營利組織和其他組織，施加新的規則。 

Microsoft 產品和服務目前均可幫助您符合 GDPR 需求。 在[信任中心](https://www.microsoft.com/trustcenter)深入了解 Microsoft 隱私權原則。

Azure AD Connect Health 會監視內部部署身分識別基礎結構和同步處理服務。 它也可向您提供深入資訊和顯示警示。 Microsoft 承諾會在 2018 年 5 月開始強制實施時讓所有雲端服務皆遵守 GDPR，以及在其契約承諾中提供 GDPR 相關保證。 

>[!NOTE] 
> 本文概述 Azure AD Connect Health 中的 GDPR 合規性。 如需有關 Azure AD Connect 中 GDPR 合規性的資訊，請參閱 [GDPR 合規性與 Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md)。

## <a name="gdpr-classification"></a>GDPR 分類
Azure AD Connect Health 會歸類到 GDPR 分類的**資料處理器**類別。 作為資料處理器管線，服務會對重要合作夥伴和終端取用者提供資料處理服務。 Azure AD Connect Health 不會產生使用者資料，也不會自行控制要收集哪些個人資料以及如何使用這些資料。 在 Azure AD Connect Health 中，資料的擷取、彙總、分析和報告都會以現有內部部署資料來作為基礎。 

## <a name="data-retention-policy"></a>資料保留原則
Azure AD Connect Health 不會針對超過 30 天的資料產生報告、執行分析或提供見解。 因此，Azure AD Connect Health 不會儲存、處理或保留任何資料達 30 天以上。 這項設計符合 GDPR 法規、Microsoft 隱私權合規性規定和 Azure AD 資料保留原則。 

伺服器如果連續 30 天有作用中「健全狀況服務的資料並非最新」**錯誤**警示，即表示在該時間範圍內，沒有任何資料抵達 Connect Health。 這些伺服器將會被停用，而不會顯示在 Connect Health 入口網站中。 若要重新啟用這些伺服器，您必須[將健康情況代理程式解除安裝後再重新安裝](active-directory-aadconnect-health-agent-install.md)。 請注意，這並不適用於具有相同警示類型的**警告**。 警告代表的是您收到警示的伺服器中遺失部分資料。 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>在 Azure AD Connect Health 中停用資料收集和監視
Azure AD Connect Health 可讓您停止針對每個個別監視的伺服器或針對一個所監視服務的執行個體收集資料。 例如，您可以針對使用 Azure AD Connect Health 所監視的個別 ADFS (Active Directory 同盟服務) 伺服器停止資料收集。 您也可以針對使用 Azure AD Connect Health 所監視的整個 ADFS 執行個體停止資料收集。 當您選擇這樣做時，系統就會在停止資料收集後從 Azure AD Connect Health 入口網站中刪除對應的伺服器。 

>[!IMPORTANT]
> 您需要 Azure AD 全域系統管理員權限或 RBAC 的參與者角色，才能從 Azure AD Connect Health 刪除所監視的伺服器。
>
> 從 Azure AD Connect Health 移除服務或服務執行個體的動作無法復原。 

### <a name="what-to-expect"></a>有何影響？
如果您要針對個別監視的伺服器或一個所監視服務的執行個體停止資料收集和監視，請注意下列事項：

- 當您刪除所監視服務的執行個體時，系統會從入口網站的 Azure AD Connect Health 監視服務清單中移除該執行個體。 
- 當您刪除所監視的伺服器或所監視服務的執行個體時，Health 代理程式「不會」解除安裝，也「不會」從您的伺服器中移除。 Health 代理程式的設定是不要將資料傳送至 Azure AD Connect Health。 您必須在先前所監視的伺服器上手動解除安裝 Health 代理程式。
- 如果您在執行此步驟之前還未解除安裝 Health 代理程式，您可能會在伺服器上看到與 Health 代理程式相關的錯誤事件。
- 根據 Microsoft Azure 資料保留原則，系統會刪除屬於所監視服務執行個體的所有資料。

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>對所監視的伺服器停用資料收集和監視
請參閱[如何從 Azure AD Connect Health 移除伺服器](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service)。

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>對所監視服務的執行個體停用資料收集和監視
請參閱[如何從 Azure AD Connect Health 刪除服務執行個體](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service)。


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>在 Azure AD Connect Health 中重新啟用資料收集和監視
若要在 Azure AD Connect Health 中對先前刪除的所監視服務重新啟用監視，您必須在所有伺服器上先解除安裝再[重新安裝 Health 代理程式](active-directory-aadconnect-health-agent-install.md)。


## <a name="next-steps"></a>後續步驟
* [在信任中心檢閱 Microsoft 隱私權原則](https://www.microsoft.com/trustcenter)
* [Azure AD Connect 和 GDPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
