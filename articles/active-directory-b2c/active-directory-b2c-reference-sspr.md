---
title: Azure Active Directory B2C：自助式密碼重設 | Microsoft Docs
description: 此主題示範如何在 Azure Active Directory B2C 中為您的取用者設定自助式密碼重設
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f38473989f90bfe6d35bffb17a02a892ad08cf5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C：為您的取用者設定自助式密碼重設
自助式密碼重設功能可讓您的取用者 (已註冊本機帳戶的使用者) 自行重設自己的密碼。 這可大幅減輕支援人員的負擔，特別是在您的應用程式具有數百萬名定期使用的取用者時更是如此。 目前僅支援使用已驗證的電子郵件地址做為復原方法。 我們將在未來新增其他復原方法 (例如已驗證的電話號碼、安全性問題等)。

> [!NOTE]
> 本文適用於登入原則內容中使用的自助式密碼重設。 如果您需要從應用程式叫用的可完全自訂密碼重設原則，請參閱 [這篇文章](active-directory-b2c-reference-policies.md#create-a-password-reset-policy)。
> 
> 

依預設，您的目錄並不會開啟自助式密碼重設。 使用下列步驟將其開啟：

1. 以訂用帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。 此為您建立目錄時所用的工作或學校帳戶，或者當時所用的 Microsoft 帳戶。
2. 在左側導覽列中，開啟 Active Directory。
3. 選取 [屬性] 。
4. 向下捲動至 [已啟用自助式密碼重設] 區段，並將它切換至 [全部]。 
5. 按一下頁面頂端的 [儲存]。 大功告成！

如果要進行測試，請針對所有將本機帳戶作為識別提供者的登入原則使用 [立即執行] 功能。 在本機帳戶登入頁面上 (您輸入電子郵件地址和密碼，或使用者名稱和密碼的頁面)，按一下 [無法存取您的帳戶？] 驗證取用者體驗。

> [!NOTE]
> 您可以使用 [公司商標功能](../active-directory/customize-branding.md)自訂自助式密碼重設頁面。
> 
> 

