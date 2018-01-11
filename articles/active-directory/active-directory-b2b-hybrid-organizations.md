---
title: "混合式組織的 azure Active Directory B2B 共同作業 |Microsoft 文件"
description: "提供協力廠商存取本機和雲端資源，Azure AD B2B 共同作業"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>混合式組織的 azure Active Directory B2B 共同作業

在混合式中組織，其中同時有內部部署和雲端資源，您可能想要授與外部夥伴存取本機和雲端資源。 本機資源的存取，您可以管理夥伴的帳戶在本機內部部署 Active Directory 環境中。 夥伴使用其夥伴帳戶認證登入來存取您的組織資源。 若要讓夥伴存取雲端資源，使用這些相同的認證，您現在可以使用 Azure Active Directory (Azure AD) 連線同步至雲端的協力電腦帳戶做為 Azure AD B2B 的使用者 (亦即，使用者與 UserType = 客體)。

## <a name="identify-unique-attributes-for-usertype"></a>UserType 識別唯一的屬性

啟用同步處理的 UserType 屬性之前，您必須先決定如何衍生 UserType 屬性從內部部署 Active Directory。 換句話說，在內部部署環境中的參數都是唯一您外部共同作業者？ 判斷參數，可從您自己的組織成員的辨別這些外部共同作業者。

這兩種常見方法是：

- 指定要做為來源屬性的未使用在內部部署 Active Directory 屬性 (例如，extensionAttribute1)。 
- 或者，衍生自其他屬性的 UserType 屬性的值。 例如，在您想要同步處理所有的使用者以 Guest，如果其在內部部署 Active Directory UserPrincipalName 屬性結束與網域 *@partners.fabrikam123.org* 。
 
如需詳細的屬性需求，請參閱[啟用同步處理的 UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)。 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>設定 Azure AD Connect 同步處理至雲端的使用者

識別唯一的屬性之後，您可以設定 Azure AD Connect 同步處理至雲端這些使用者做為 Azure AD B2B 的使用者 (亦即，使用者與 UserType = 客體)。 從授權的觀點，這些使用者是透過 Azure AD B2B 共同作業邀請程序建立 B2B 使用者無法區別。

實作指示，請參閱[啟用同步處理的 UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)。

## <a name="next-steps"></a>後續步驟

- 如需 Azure AD B2B 共同作業的概觀，請參閱[什麼是 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- 如需 Azure AD Connect 的概觀，請參閱[整合內部部署目錄與 Azure Active Directory](connect/active-directory-aadconnect.md)。

