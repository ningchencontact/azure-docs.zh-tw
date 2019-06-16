---
title: B2B 共同作業-Azure Active Directory 的限制 |Microsoft Docs
description: Azure Active Directory B2B 共同作業目前的限制
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: b45277c89193c51f70836bcef8a21636fc9c7973
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113131"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 共同作業的限制
Azure Active Directory (Azure AD) B2B 共同作業目前受限於本文所述的限制。

## <a name="possible-double-multi-factor-authentication"></a>可能的雙重多重要素驗證
使用 Azure AD B2B，您便可以在資源組織 (邀請組織) 強制執行多重要素驗證。 這種方法的原因會詳述[B2B 共同作業使用者的條件式存取](conditional-access.md)。 如果合作夥伴已設定並強制使用 Multi-Factor Authentication，其使用者可能必須在其所屬組織執行一次驗證，然後在您的組織中再次執行驗證。

## <a name="instant-on"></a>即時
在 B2B 共同作業流程中，我們會新增使用者到目錄中，並在邀請兌換與應用程式指派等期間動態更新它們。 更新與寫入通常會在一個目錄執行個體中進行，而且必須複寫到所有執行個體。 在所有執行個體都更新後，複寫作業便已完成。 有時，在某個執行個體中寫入或更新物件，但另一個執行個體呼叫擷取此物件時，就會發生複寫延遲問題。 如果發生該情況，請重新整理或重試來提供幫助。 如果您正在使用我們的 API 來撰寫應用程式，則採用某種讓步來重試是可減輕此問題的良好防禦性做法。

## <a name="azure-ad-directories"></a>Azure AD 目錄
Azure AD B2B 受限於 Azure AD 服務目錄限制。 如需使用者可建立的目錄數目以及使用者或來賓使用者可屬於的目錄數目詳細資訊，請參閱 [Azure AD 服務限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。

## <a name="national-clouds"></a>國家/地區雲端
[國家/地區雲端](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)是 Azure 的實體隔離的執行個體。 B2B 共同作業不支援跨國家雲端界限。 比方說，如果您的 Azure 租用戶是公用的全域定域機組中，您就無法邀請國家雲端中使用者的帳戶。 共同作業與使用者，要求他們另一個電子郵件地址，或為其建立成員的使用者帳戶，在您的目錄中。

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的文章：

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [委派 B2B 共同作業邀請](delegate-invitations.md)

