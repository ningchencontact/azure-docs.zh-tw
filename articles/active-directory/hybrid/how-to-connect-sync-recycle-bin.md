---
title: Azure AD Connect 同步：啟用 AD 資源回收筒 | Microsoft Docs
description: 本主題建議對 Azure AD Connect 使用 AD 資源回收筒功能。
services: active-directory
keywords: AD 資源回收筒, 意外刪除, 來源錨點
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4836ffc8c6ab013ef5ad7a661db0df3254b6d4e1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468713"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect 同步：啟用 AD 資源回收筒
建議您為會同步處理至 Azure AD 的內部部署 Active Directory 啟用 AD 資源回收筒功能。 

如果您不小心刪除內部部署 AD 使用者物件，並使用此功能還原該物件，Azure AD 會還原對應的 Azure AD 使用者物件。  如需 AD 資源回收筒功能的相關資訊，請參閱[還原已刪除之 Active Directory 物件的案例概觀](https://technet.microsoft.com/library/dd379542.aspx)一文。

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>啟用 AD 資源回收筒的好處
這項功能可協助還原 Azure AD 使用者物件，方法是執行下列動作︰

* 如果您不小心刪除內部部署 AD 使用者物件，系統會在下一個同步處理循環刪除對應的 Azure AD 使用者物件。 根據預設，Azure AD 會讓已刪除的 Azure AD 使用者物件保持 30 天的虛刪除狀態。

* 如果您已啟用內部部署 AD 資源回收筒功能，您可以還原已刪除的內部部署 AD 使用者物件，而不必變更其來源錨點值。 當復原後的內部部署 AD 使用者物件同步處理至 Azure AD 時，Azure AD 會還原對應的虛刪除 Azure AD 使用者物件。 如需來源錨點屬性的相關資訊，請參閱 [Azure AD Connect：設計概念](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor)一文。

* 如果您未啟用內部部署 AD 資源回收筒功能，您可能需要建立 AD 使用者物件來取代已刪除的物件。 如果您將 Azure AD Connect 同步處理服務設定為使用系統產生的 AD 屬性 (例如 ObjectGuid) 作為來源錨點屬性，新建立的 AD 使用者物件所具有的來源錨點值不會和已刪除之 AD 使用者物件的值相同。 當新建立的 AD 使用者物件同步處理至 Azure AD 時，Azure AD 會建立新的 Azure AD 使用者物件，而不是還原虛刪除的 Azure AD 使用者物件。

> [!NOTE]
> 根據預設，Azure AD 會讓已刪除的 Azure AD 使用者物件保持 30 天的虛刪除狀態，再將其永久刪除。 不過，系統管理員也可以提早刪除這類物件。 物件在永久刪除後就無法再復原，即使您已啟用內部部署 AD 資源回收筒功能也是一樣。

>[!NOTE]
>之前，當使用者從內部部署同步處理範圍中移除並在雲端中刪除時，帳戶的 DirSyncEnabled 狀態已錯誤設定為 "False"。 如果之後手動從 Azure AD 資源回收筒還原該使用者，則會顯示「僅限雲端」帳戶的不正確狀態。 現在已修正此問題，若使用者從同步範圍中移除，然後虛刪除，再手動從 Azure AD 資源回收筒還原，DirSyncEnabled 狀態的值一律會保持 “True”。

## <a name="next-steps"></a>後續步驟
**概觀主題**

* [Azure AD Connect 同步：了解並自訂同步處理](how-to-connect-sync-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)
