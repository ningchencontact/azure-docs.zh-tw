---
title: 在 Azure Active Directory 中停用企業應用程式的使用者登入 | Microsoft Docs
description: 如何在 Azure Active Directory 中停用企業應用程式，讓任何使用者都無法登入它
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a168c3415996ff97137c4e312348f74a48e9531b
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564987"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中停用企業應用程式的使用者登入
它很容易就能停用企業應用程式，因此沒有任何使用者可以登入 Azure Active Directory (Azure AD) 中。 您需要適當的權限，才能管理企業應用程式。 而且，您必須是目錄全域管理員。

## <a name="how-do-i-disable-user-sign-ins"></a>如何停用使用者登入？
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
1. 選取 [所有服務]，在文字方塊中輸入 **Azure Active Directory**，然後選取 **Enter** 鍵。
1. 在  **Azure Active Directory** -  ***directoryname***窗格 （也就是 Azure AD 窗格為您管理的目錄），選取**企業應用程式**.
1. 在 [**企業應用程式-所有應用程式**] 窗格中，您會看到一份可以管理的應用程式。 選取應用程式。
1. 在 [appname] 窗格 (亦即標題中含有所選應用程式之名稱的窗格) 上，選取 [屬性]。
1. 在 [appname - 屬性] 窗格上，針對 [為使用者啟用登入?] 選取 [否]。
1. 選取 [儲存]  命令。

## <a name="next-steps"></a>後續步驟
* [查看我的所有群組](../fundamentals/active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
* [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
* [變更企業應用程式的名稱或標誌](change-name-or-logo-portal.md)
