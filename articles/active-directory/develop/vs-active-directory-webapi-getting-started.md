---
title: 開始在 Visual Studio WebApi 專案中使用 Azure AD
description: 使用 Visual Studio 已連接服務連接 Azure AD 或建立 Azure AD 後，如何在 WebApi 專案中開始使用 Azure Active Directory
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
origin.date: 03/12/2018
ms.date: 09/03/2018
ms.author: v-junlch
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 662d958fc7ef6fe5c9d0e61a1d8e48983d10196f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60353800"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>開始使用 Azure Active Directory (WebApi 專案)

> [!div class="op_single_selector"]
> - [開始使用](vs-active-directory-webapi-getting-started.md)
> - [發生什麼情形](vs-active-directory-webapi-what-happened.md)

本文提供您在透過 Visual Studio 的 [專案] > [連線服務] 命令將 Active Directory 新增至 ASP.NET WebAPI 專案之後所需的其他指引。 如果您尚未將服務新增至專案，您可以隨時執行此動作。

請參閱[我的 WebApI 專案發生什麼情形？](vs-active-directory-webapi-what-happened.md)，了解您的專案在新增連線服務時所做的變更。

## <a name="requiring-authentication-to-access-controllers"></a>存取控制器之前需要驗證

專案中的所有控制器都會加上 `[Authorize]` 屬性作為裝飾。 此屬性要求使用者必須經過驗證，才能存取這些控制器所定義的 API。 若要允许匿名访问控制器，请从控制器删除此属性。 如果想要更详细地设置这些权限，请将该属性应用到需要身份验证的每个方法，而不是将它应用到控制器类。

## <a name="next-steps"></a>後續步驟

- [Azure Active Directory 的驗證案例](authentication-scenarios.md)
- [將「使用 Microsoft 登入」新增至 ASP.NET Web 應用程式](quickstart-v1-aspnet-webapp.md)

<!-- Update_Description: link update -->