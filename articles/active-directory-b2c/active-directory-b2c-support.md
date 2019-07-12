---
title: Azure Active Directory B2C 的支援 | Microsoft Docs
description: 如何提出 Azure Active Directory B2C 的支援要求。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ae5d04b7fac89cefb40d5542e2c6e83533afcfce
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835764"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C：檔案支援要求
您可使用下列步驟，在 Azure 入口網站上提出 Azure Active Directory (Azure AD) B2C 的支援要求：

1. 從 B2C 租用戶切換至另一個擁有相關聯 Azure 訂用帳戶的租用戶。 後者通常為員工租用戶，或是您註冊 Azure 訂用帳戶時所建立的預設租用戶。 若要深入了解，請參閱 [Azure 訂用帳戶如何與 Azure AD 有關](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

    ![使用租用戶選取項目反白顯示的 azure 入口網站](./media/active-directory-b2c-support/support-switch-dir.png)

3. 切換租用戶之後，請按一下 [說明 + 支援]  。

    ![說明 + 支援 圖格反白顯示在 Azure 入口網站](./media/active-directory-b2c-support/support-support.png)

4. 按一下 [新增支援要求]  。

    ![反白顯示在 Azure 入口網站中的新支援要求 磚](./media/active-directory-b2c-support/support-new.png)
5. 在 [基本]  刀鋒視窗中，使用這些詳細資料，然後按 [下一步]  。

   * [問題類型]  是 [技術]  。
   * 選擇適當的 [訂用帳戶]  。
   * [服務]  是 [Active Directory]  。
   * 選擇適當的 [支援方案]  。 如果您沒有任何方案，可以在[這裡](https://azure.microsoft.com/support/plans/)註冊一個。

     ![反白顯示在 Azure 入口網站中的下一步 按鈕的基本頁面](./media/active-directory-b2c-support/support-basics.png)
6. 在 [問題]  刀鋒視窗中，使用這些詳細資料，然後按 [下一步]  。

   * 選擇適當的 [嚴重性]  層級。
   * [問題類型]  是 [B2C]  。
   * 選擇適當的 [類別]  。
   * 在 [詳細資料]  欄位中描述您的問題。 提供詳細資料，例如 B2C 租用戶名稱、問題的描述、錯誤訊息、相互關聯識別碼 (如果有的話) 等等。
   * 在 [時間範圍]  欄位中，提供發生問題的日期和時間 (包括時區)。
   * 在 [檔案上傳]  底下，上傳所有螢幕擷取畫面以及您認為對解決問題有幫助的檔案。

     ![問題 頁面上反白顯示在 Azure 入口網站中的下一步 按鈕](./media/active-directory-b2c-support/support-problem.png)
7. 在 [連絡資訊]  刀鋒視窗中，加入您的連絡資訊。 按一下 [建立]  。

    ![使用 Azure 入口網站中反白顯示的 [建立] 按鈕的連絡資訊頁面](./media/active-directory-b2c-support/support-contact.png)
8. 提交支援要求之後，您可在「開始面板」上按一下 [說明 + 支援]  ，然後按一下 [管理支援要求]  。

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>已知問題︰在 B2C 租用戶的內容中提出支援要求
若您遺漏上述的步驟 2，且嘗試在 B2C 租用戶的內容中建立支援要求，您將會看到以下錯誤。

> [!IMPORTANT]
> 請不要嘗試在 B2C 租用戶中註冊新的 Azure 訂用帳戶。
>
>

![支援 - 無訂用帳戶](./media/active-directory-b2c-support/support-no-sub.png)

