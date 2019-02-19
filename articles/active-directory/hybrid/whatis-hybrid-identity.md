---
title: 使用 Azure Active Directory 連接 Active Directory。 | Microsoft Docs
description: Azure AD Connect 會整合您的內部部署目錄與 Azure Active Directory。 這可讓您為與 Azure AD 整合的 Office 365、Azure 和 SaaS 應用程式提供通用身分識別。
keywords: Azure AD Connect 簡介, Azure AD Connect 概觀, 何謂 Azure AD Connect, 安裝 active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: df5d42135c538ca3d0a63252805efb44cef7e8b0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190461"
---
# <a name="what-is-hybrid-identity"></a>什麼是混合式身分識別？ 

現今的企業和公司混合使用內部部署和雲端應用程式的情形越來越多。  使用者需要在內部部署環境和雲端存取這些應用程式。 這項需求已成為具有挑戰性的案例。 

Microsoft 的身分識別解決方案可跨越內部部署和雲端架構功能。  這些解決方案會建立通用使用者身分識別，以便進行所有資源的驗證和授權 (不論位於何處)。 我們稱之為**混合式身分識別**。

若要達到混合式身分識別，可以視您的案例而定，使用下列其中一種驗證方法。   方法有三種： 

- **[密碼雜湊同步處理 (PHS)](whatis-phs.md)**  
- **[傳遞驗證 (PTA)](how-to-connect-pta.md)**  
- **[同盟](whatis-fed.md)** 

這些驗證方法也會提供[單一登入](how-to-connect-sso.md)功能。  使用者位於連線到貴公司網路的公司裝置時，單一登入功能會自動將他們登入。

如需其他資訊，請參閱[針對 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)。 

## <a name="common-scenarios-and-recommendations"></a>常見案例和建議 

以下是一些常見的混合式身分識別和存取管理案例，其中包含各自適合的混合式身分識別選項建議。 

|我需要：|PHS 和 SSO<sup>1</sup>| PTA 和 SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|自動將我的內部部署 Active Directory 中建立的新使用者、連絡人及群組帳戶同步至雲端。|![建議](./media/whatis-hybrid-identity/ic195031.png)| ![建議](./media/whatis-hybrid-identity/ic195031.png) |![建議](./media/whatis-hybrid-identity/ic195031.png)| 
|設定適用於 Office 365 混合式案例的租用戶。|![建議](./media/whatis-hybrid-identity/ic195031.png)| ![建議](./media/whatis-hybrid-identity/ic195031.png) |![建議](./media/whatis-hybrid-identity/ic195031.png)| 
|讓我的使用者可以使用其內部部署密碼登入及存取雲端服務。|![建議](./media/whatis-hybrid-identity/ic195031.png)| ![建議](./media/whatis-hybrid-identity/ic195031.png) |![建議](./media/whatis-hybrid-identity/ic195031.png)| 
|使用公司認證實作單一登入。|![建議](./media/whatis-hybrid-identity/ic195031.png)| ![建議](./media/whatis-hybrid-identity/ic195031.png) |![建議](./media/whatis-hybrid-identity/ic195031.png)|  
|確定雲端中未儲存任何密碼雜湊。| |![建議](./media/whatis-hybrid-identity/ic195031.png)|![建議](./media/whatis-hybrid-identity/ic195031.png)| 
|啟用雲端多重要素驗證解決方案。| |![建議](./media/whatis-hybrid-identity/ic195031.png)|![建議](./media/whatis-hybrid-identity/ic195031.png)| 
|啟用內部部署多重要素驗證解決方案。| | |![建議](./media/whatis-hybrid-identity/ic195031.png)| 
|對使用者支援智慧卡驗證。<sup>4</sup>| | |![建議](./media/whatis-hybrid-identity/ic195031.png)| 
|在 Office 入口網站中和 Windows 10 桌面上顯示密碼到期通知。| | |![建議](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> 單一登入的密碼雜湊同步處理。 
> 
> <sup>2</sup> 傳遞驗證和單一登入。  
> 
> <sup>3</sup> 與 AD FS 同盟的單一登入。  
>  
> <sup>4</sup> AD FS 可與您的企業 PKI 整合，以允許使用憑證登入。 這些憑證可以是透過信任的佈建管道 (例如 MDM、GPO、智慧卡憑證 (包括 PIV/CAC 卡) 或 Hello for Business (cert-trust)) 部署的軟性憑證。 如需智慧卡驗證支援的詳細資訊，請參閱[這個部落格](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)。 
> 

## <a name="next-steps"></a>後續步驟 

- [什麼是 Azure AD Connect 和 Connect Health？](whatis-azure-ad-connect.md) 
- [什麼是密碼雜湊同步處理 (PHS)？](whatis-phs.md) 
- [什麼是傳遞驗證 (PTA)？](how-to-connect-pta.md) 
- [什麼是同盟？](whatis-fed.md) 
- [何謂單一登入？](how-to-connect-sso.md) 

