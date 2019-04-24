---
title: 登录身份验证期间的用户名查找 - Azure Active Directory | Microsoft Docs
description: 屏幕上的消息如何反映登录期间的用户名查找
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b6846c5f907c41db16e99883be7041a68357586
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60468830"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory 登入頁面的主領域探索

我們正在變更 Azure Active Directory (Azure AD) 的登入行為，來為新的驗證方法騰出空間並改善可用性。 登入期間，Azure AD 會判斷使用者應在何處進行驗證。 Azure AD 會讀取組織和使用者設定，來針對於登入頁面輸入的使用者名稱做出最佳的決策。 此技术为将来支持 FIDO 2.0 等附加凭据的无密码登录进一步打下了基础。

## <a name="home-realm-discovery-behavior"></a>主领域发现的行为

一直以来，主领域发现都是由登录时提供的域调控的，或者由某些传统应用程序的主领域发现策略调控的。 比方說，我們探索行為中的 Azure Active Directory 使用者可以輸入其使用者名稱，但仍會在其組織的認證集合畫面會送達。 当用户正确提供组织的域名“contoso.com”时，就会发生这种情况。 此行为不允许对个人用户的体验进行精细的自定义。

为了支持更多样化的凭据并提高可用性，现已更新登录期间的 Azure Active Directory 用户名查找行为。 新的行为可以根据登录页上输入的用户名，通过读取租户和用户级别的设置来做出明智的决策。 为了实现此目的，Azure Active Directory 将检查登录页上输入的用户名是否在用户的指定域中存在，或者将用户重定向到可输入凭据的位置。

此项工作的另一个好处是改进了错误消息。 下面是登录到仅支持 Azure Active Directory 用户的应用程序时，显示的一些经过改进的错误消息示例。

1. 键入错误的用户名，或者用户名尚未同步到 Azure AD：
  
    ![键入错误的用户名，或者找不到该用户名](./media/signin-realm-discovery/typo-username.png)
  
2. 键入错误的域名：
  
    ![键入错误的域名，或者找不到该域名](./media/signin-realm-discovery/typo-domain.png)
  
3. 用户尝试使用已知的使用者域登录：
  
    ![使用已知的使用者域登录](./media/signin-realm-discovery/consumer-domain.png)
  
4. 键入错误的密码，但用户名正确：  
  
    ![键入错误的密码，但用户名正确](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> 這項功能可能會影響依賴舊的網域層級主領域探索來強制同盟的同盟網域。 更新會新增同盟的網域支援的時，請參閱 <<c0> [ 主領域探索 Microsoft 365 服務的登入期間](https://azure.microsoft.com/en-us/updates/signin-hrd/)。 在此同時，某些組織有訓練員工能夠使用 Azure Active Directory 中不存在，但是包含適當的網域名稱，使用者名稱登入，因為網域名稱將使用者路由目前到其組織的網域的端點。 新的登录行为不允许此操作。 系统会告知用户更正用户名，且不允许他们使用 Azure Active Directory 中不存在的用户名登录。
>
> 如果你或你的组织在实践中依赖于旧行为，则组织管理员必须更新员工登录与身份验证文档，并为员工提供培训，让他们使用其 Azure Active Directory 用户名登录。
  
如果您有新的行為方面的疑慮，讓您的註解中**意見反應**一節。  

## <a name="next-steps"></a>後續步驟

[自訂您登入的品牌](../fundamentals/add-custom-domain.md)
