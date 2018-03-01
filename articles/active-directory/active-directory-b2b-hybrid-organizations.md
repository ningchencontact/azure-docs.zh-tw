---
title: "混合式組織的 Azure Active Directory B2B 共同作業 | Microsoft Docs"
description: "透過 Azure AD B2B 共同作業對夥伴提供本機和雲端資源的存取權"
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
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>混合式組織的 Azure Active Directory B2B 共同作業

在同時擁有內部部署和雲端資源的混合式組織中，您可以對外部夥伴提供本機和雲端資源的存取權。 針對本機資源的存取權，您可以在內部部署 Active Directory 環境的本機中管理夥伴的帳戶。 夥伴可以使用其夥伴帳戶認證來登入，以存取貴組織的資源。 若要使用同樣的認證對夥伴提供雲端資源的存取權，您現在可以使用 Azure Active Directory (Azure AD) Connect 將夥伴帳戶同步至雲端來作為 Azure AD B2B 使用者 (亦即，UserType = Guest 的使用者)。

## <a name="identify-unique-attributes-for-usertype"></a>識別 UserType 的獨特屬性

在啟用 UserType 屬性的同步處理之前，您必須先決定要如何從內部部署 Active Directory 衍生 UserType 屬性。 換句話說，內部部署環境中的哪些參數是外部共同作業者所獨有的？ 請決定可供區分這些外部共同作業者與貴組織成員的參數。

針對此一目的，常見的方法有二：

- 指定未使用的內部部署 Active Directory 屬性 (例如 extensionAttribute1) 來作為來源屬性。 
- 或者，從其他屬性衍生 UserType 屬性的值。 例如，如果使用者的內部部署 Active Directory UserPrincipalName 屬性是以 *@partners.fabrikam123.org* 網域作為結尾，您就需要以 Guest 身分來同步處理所有使用者。
 
如需詳細的屬性需求，請參閱[啟用 UserType 的同步處理](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)。 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>設定 Azure AD Connect 以將使用者同步至雲端

在識別獨有屬性後，您可以設定 Azure AD Connect 來將這些使用者同步處理至雲端以作為 Azure AD B2B 使用者 (亦即，UserType = Guest 的使用者)。 從授權的觀點來看，這些使用者與透過 Azure AD B2B 共同作業邀請程序所建立的 B2B 使用者並無不同。

如需實作指示，請參閱[啟用 UserType 的同步處理](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)。

## <a name="next-steps"></a>後續步驟

- 如需 Azure AD B2B 共同作業的概觀，請參閱[何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- 如需 Azure AD Connect 的概觀，請參閱[整合您的內部部署目錄與 Azure Active Directory](connect/active-directory-aadconnect.md)。

