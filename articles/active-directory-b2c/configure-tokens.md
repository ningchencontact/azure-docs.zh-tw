---
title: 設定權杖-Azure Active Directory B2C |Microsoft Docs
description: 了解如何設定 Azure Active Directory B2C 中的權杖的存留期和相容性設定。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e1163c88a100ebb7500607475ab5740557904137
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511338"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>設定 Azure Active Directory B2C 中的權杖

在本文中，了解如何設定[存留期和語彙基元的相容性](active-directory-b2c-reference-tokens.md)Azure Active Directory (Azure AD) B2C 中。

## <a name="prerequisites"></a>必要條件

[建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。

## <a name="configure-token-lifetime"></a>設定權杖存留期

您可以設定權杖存留期的任何使用者流程。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 請確定您使用包含 Azure AD B2C 租用戶的目錄。 選取 **目錄和訂用帳戶篩選**上方功能表中，然後選擇包含您的 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
4. 選取  **（原則） 的使用者流程**。
5. 開啟您先前建立的使用者流程。 
6. 選取 [屬性]  。
7. 底下**權杖存留期**，調整以符合您的應用程式需求的下列屬性：

    ![設定權杖存留期](./media/configure-tokens/token-lifetime.png)

8. 按一下 [檔案]  。

## <a name="configure-token-compatibility"></a>設定權杖相容性

1. 選取  **（原則） 的使用者流程**。
2. 開啟您先前建立的使用者流程。 
3. 選取 [屬性]  。
4. 底下**k 相容性設定**，調整以符合您的應用程式需求的下列屬性：

    ![設定權杖相容性](./media/configure-tokens/token-compatibility.png)

5. 按一下 [檔案]  。

## <a name="next-steps"></a>後續步驟

深入了解如何[使用存取權杖](active-directory-b2c-access-tokens.md)。



