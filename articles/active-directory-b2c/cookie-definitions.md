---
title: Cookie 定義-Azure Active Directory B2C |Microsoft Docs
description: 提供在 Azure Active Directory B2C 中使用的 cookie 的定義。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7864320b71416d1b06661b8ae96c6113962250dd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703985"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的 cookie 定義

下表列出 Azure Active Directory B2C 中使用的 cookie。

| 名稱 | 網域 | 到期 | 目的 |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | 跨租用戶會保留使用者成員資格資料。 使用者的租用戶是成員和層級的成員資格 （系統管理員或使用者）。 |
| x-ms-cpim-slice | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | 用來將要求路由至適當的生產執行個體。 |
| x-ms-cpim-trans | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | 用來追蹤交易 （至 Azure AD B2C 的驗證要求的數目），以及目前的交易。 |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | 用來維護 SSO 工作階段。 |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束[瀏覽器工作階段](active-directory-b2c-token-session-sso.md)，驗證成功 | 用於維護要求的狀態。 |
| x-ms-cpim-csrf | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | 用於 CRSF 保護跨網站偽造要求語彙基元。 |
| x-ms-cpim-dc | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | 使用 Azure AD B2C 網路路由。 |
| x-ms-cpim-ctx | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | Context |
| x-ms-cpim-rp | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | 用於將成員資格資料儲存為資源提供者的租用戶。 |
| x-ms-cpim-rc | login.microsoftonline.com、 b2clogin.com、 加上品牌的網域 | 結束的[瀏覽器工作階段](active-directory-b2c-token-session-sso.md) | 用來儲存轉送 cookie。 |

