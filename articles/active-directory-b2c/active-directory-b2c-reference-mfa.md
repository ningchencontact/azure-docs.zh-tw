---
title: Azure Active Directory B2C 中的 Multi-Factor Authentication | Microsoft Docs
description: 如何在受 Azure Active Directory B2C 保護的取用者導向應用程式中啟用 Multi-Factor Authentication。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: eabae0f3575719c6cb93affefe0a393dd13d1439
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014001"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中啟用多重要素驗證 | Microsoft Docs

Azure Active Directory (Azure AD) B2C 直接整合 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)，讓您能夠針對應用程式的註冊與登入使用體驗，增添第二層安全性。 您不需要撰寫單一行程式碼，即可啟用多重要素驗證。 如果您已經建立註冊和登入原則，仍然可以啟用多重要素驗證。

此功能可協助應用程式處理諸如下列的各種案例：

- 您在存取某一個應用程式時不需要多重要素驗證，但在存取另一應用程式時需要它。 例如，客戶可以使用社交或本機帳戶登入汽車保險應用程式，但必須先驗證電話號碼，才能存取同個目錄中註冊的家庭保險應用程式。
- 一般而言，您在存取應用程式時不需要多重要素驗證，但在存取其中的敏感部分資訊時則需要它。 例如，客戶可以使用社交或本機帳戶登入銀行應用程式來檢查帳戶餘額，但必須先完成電話號碼驗證，才能嘗試進行轉帳匯款作業。

## <a name="set-multi-factor-authentication"></a>設定多重要素驗證

當您建立原則時，可以選擇啟用多重要素驗證。

![設定多重要素驗證](./media/active-directory-b2c-reference-mfa/add-policy.png)

將 [狀態] 設定為 [開啟]。

您可以在原則上使用 [立即執行] 以驗證體驗。 請確認下列狀況：

在進行多重要素驗證步驟之前，已在租用戶中建立客戶帳戶。 在步驟執行過程中，系統會要求客戶提供電話號碼進行驗證。 若驗證成功，會將電話號碼附加至帳戶以供之後使用。 即使客戶取消或卸除，下次登入時系統可能會要求客戶再度驗證電話號碼 (已啟用多重要素驗證)。

## <a name="add-multi-factor-authentication"></a>新增多重要素驗證

您可以在先前建立的原則上啟用多重要素驗證。 

啟用多重要素驗證：

1. 開啟原則，然後選取 [編輯]。 
2. 選取 [多重要素驗證]
3. 將 [狀態] 設定為 [開啟]。
4. 按一下頁面頂端的 [儲存]。


