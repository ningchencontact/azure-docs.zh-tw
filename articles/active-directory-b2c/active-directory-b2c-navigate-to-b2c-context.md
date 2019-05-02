---
title: 在 Azure Active Directory B2C 中切換至 B2C 租用戶 | Microsoft Docs
description: 如何切換至您的 Active Directory B2C 租用戶環境。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ee6d85788211315412b7cbd3c82532dcd45162f7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697698"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>切換至您的 Azure AD B2C 租用戶

若要設定 Azure AD B2C，您需要在您的 Azure AD B2C 租用戶環境中。

## <a name="log-into-azure-ad-b2c-tenant"></a>登入您的 Azure AD B2C 租用戶

若要瀏覽至您的 Azure AD B2C 租用戶，您必須以 Azure AD B2C 租用戶的全域管理員身分登入 Azure 入口網站。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下右上角您的電子郵件地址或圖片來切換租用戶。
1. 在顯示的 `Directory` 清單中，選取您想要管理的 Azure AD B2C 租用戶。

Azure 入口網站會重新整理。  現在您已在您的 Azure AD B2C 租用戶環境中登入 Azure 入口網站。

## <a name="navigate-to-the-b2c-features-pane"></a>瀏覽至 B2C 功能窗格

1. 在左側導覽中按一下 [瀏覽]。
1. 按一下 [所有服務]，然後在左導覽窗格中搜尋 `Azure AD B2C`。  (若要釘選到左側的「開始面板」，請按一下 Azure AD B2C 左邊的星形)
1. 按一下 [Azure AD B2C]  以存取 B2C 功能窗格。
   
    ![浏览到 B2C 功能窗格的屏幕截图](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> 您必須是 B2C 租用戶的全域管理員，才能存取 B2C 功能窗格。 其他租用戶的全域管理員或所有租用戶的使用者均無法存取。  您可以使用 Azure 入口網站右上角中的租用戶切換器來切換至 B2C 租用戶。
