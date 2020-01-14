---
title: Cookie 定義-Azure Active Directory B2C |Microsoft Docs
description: 提供 Azure Active Directory B2C 中使用之 cookie 的定義。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930898"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的 cookie 定義

下表列出 Azure Active Directory B2C 中使用的 cookie。

| 名稱 | 網域 | 到期 | 目的 |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-系統管理員 | main.b2cadmin.ext.azure.com | [瀏覽器會話](session-behavior.md)結束 | 跨租使用者保存使用者成員資格資料。 使用者為其成員的租使用者，以及成員資格的層級（系統管理員或使用者）。 |
| x-ms-cpim-配量 | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用來將要求路由傳送至適當的生產實例。 |
| x-ms-cpim-交易 | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於追蹤交易（Azure AD B2C 的驗證要求數目）和目前的交易。 |
| x-ms-cpim-sso： {Id} | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於維護 SSO 會話。 |
| x-ms-cpim-快取： {id} _n | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束，驗證成功 | 用於維護要求狀態。 |
| x-ms-cpim-csrf | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於 CRSF 保護的跨網站要求偽造 token。 |
| x-ms-cpim-dc | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於 Azure AD B2C 網路路由。 |
| x-ms-cpim-ctx | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | Context |
| x-ms-cpim-rp | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於儲存資源提供者租使用者的成員資格資料。 |
| x-ms-cpim-rc | login.microsoftonline.com、b2clogin.com、品牌化網域 | [瀏覽器會話](session-behavior.md)結束 | 用於儲存轉送 cookie。 |
