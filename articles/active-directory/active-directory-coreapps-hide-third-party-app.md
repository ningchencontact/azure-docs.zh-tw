---
title: "隱藏的應用程式，從 Azure Active Directory 中的使用者經驗 |Microsoft 文件"
description: "如何隱藏從 Azure Active Directory 中的使用者體驗的應用程式"
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
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 667fdd45bc9eb1f01ce3883006bb29274478cb83
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>隱藏使用者經驗，在 Azure Active Directory 應用程式

如果您不想要在使用者的存取面板或 Office 365 launchers 上顯示的應用程式，但沒有選項來隱藏此應用程式磚。 此選項只適用於協力廠商應用程式 （不會由 Microsoft 所發行的應用程式）。 隱藏應用程式之後，使用者仍然有應用程式的權限，但是在應用程式啟動器中看不到這些應用程式。 您必須具備適當的權限，才能管理企業應用程式，而且必須是目錄的全域管理員。 

## <a name="hiding-an-application-from-users-end-user-experiences"></a>隱藏使用者的終端使用者體驗的應用程式
使用下列步驟來隱藏應用程式使用者的存取面板和 Office 365 應用程式 launchers

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>如何從使用者的存取面板和 O365 應用程式啟動器中隱藏第三方應用程式？

1.  使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2.  選取 [更多服務]，在文字方塊中輸入 **Azure Active Directory**，然後選取 **Enter**。
3.  在 [Azure Active Directory - *directoryname*] 畫面上 (也就是您所管理目錄的 Azure AD 畫面上)，選取 [企業應用程式]。
![企業應用程式](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  在 [企業應用程式] 畫面上，選取 [所有應用程式]。 您會看到一份您可以管理的應用程式清單。
5.  在 [企業應用程式 - 所有應用程式] 畫面上，選取一個應用程式。</br>
![企業應用程式](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  在 ***appname*** 畫面 (亦即標題中含有所選應用程式名稱的畫面) 上，選取 [屬性]。
7.  在 [*appname* - 屬性] 畫面上，針對 [是否要向使用者顯示?] 選取 [是]。
![企業應用程式](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  選取 [儲存]  命令。

## <a name="next-steps"></a>後續步驟
* [查看我的所有群組](active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)
* [從企業應用程式中移除使用者或群組指派](active-directory-coreapps-remove-assignment-azure-portal.md)
* [變更企業應用程式的名稱或標誌](active-directory-coreapps-change-app-logo-user-azure-portal.md)
