---
title: 管理對 Azure 帳單的存取 | Microsoft Docs
description: 了解如何為您的小組成員提供對 Azure 計費資訊的存取權。
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8c2843840790d1e0dbfd4a789775c6c7ceb51a54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918458"
---
# <a name="manage-access-to-billing-information-for-azure"></a>管理對 Azure 帳單資訊的存取

針對大多數的訂用帳戶，您可以從 Azure 入口網站的 [訂用帳戶]  ，將帳單資訊存取權授與您的小組成員。 如果您是具有 Enterprise 合約的 Azure 客戶 (EA 客戶) 和企業系統管理員，您可以在企業版入口網站中，將權限授與部門系統管理員和帳戶擁有者。

## <a name="give-access-to-billing"></a>授與帳單存取權

EA 客戶以外的所有人員都可以透過將下列其中一個使用者角色指派給您的小組成員，來授與 Azure 帳單資訊的存取權：

- 帳戶管理員
- 服務管理員
- 共同管理員
- 擁有者
- 參與者
- 讀取者
- 帳單讀取器

若要指派角色，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。

這些角色可以在 [Azure 入口網站](https://portal.azure.com/)中存取帳單資訊。 獲指派這些角色的人員也可以使用[計費 API](billing-usage-rate-card-overview.md)，以程式設計方式取得發票和使用量詳細資料。 如需詳細資訊，請參閱 [Azure RBAC 中的角色](../role-based-access-control/built-in-roles.md)。

### <a name="opt-in"></a> 允許使用者下載發票

您將適當的角色指派給您的小組成員之後，帳戶管理員必須存取以下載發票，Azure 入口網站中的開啟。 2016 年 12 月以前的發票僅供帳戶管理員使用。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 如果您是「帳戶管理員」，請從 Azure 入口網站中的 [[訂用帳戶] 刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)選取您的訂用帳戶。

1. 選取 [發票]  ，然後選取 [存取發票]  。

    ![螢幕擷取畫面顯示如何委派存取發票](./media/billing-manage-access/AA-optin.png)

1. 選取 [開啟]  ，然後儲存。

    ![螢幕擷取畫面顯示委派存取發票的開關](./media/billing-manage-access/AA-optinAllow.png)

「帳戶管理員」也可以設定透過電子郵件傳送發票。 若要深入了解，請參閱[透過電子郵件取得發票](billing-download-azure-invoice-daily-usage-date.md)。

## <a name="give-read-only-access-to-billing"></a>授與帳單唯讀存取權

請將「帳單讀者」角色指派給需要以唯讀方式存取訂用帳戶帳單資訊，但不需要能夠管理或建立 Azure 服務的使用者。 此角色適用於組織內負責 Azure 訂用帳戶之財務和成本管理的使用者。

如果您是 EA 客戶，帳戶擁有者或部門系統管理員可以將「帳單讀者」角色指派給小組成員。 但若要讓帳單讀者檢視部門或帳戶的帳單資訊，企業系統管理員必須在企業版入口網站中啟用 [AO 檢視費用]  或 [DA 檢視費用]  原則。

「帳單讀者」功能目前為預覽版，尚未支援非全球雲端。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 從 Azure 入口網站中的 [[訂用帳戶] 刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)選取您的訂用帳戶。

1. 選取 [存取控制 (IAM)]  。
1. 選取 [角色指派]  以檢視此訂用帳戶的所有角色指派。
1. 選取 [新增角色指派]  。
1. 在 [角色]  下拉式清單中，選擇 [帳單讀者]  。
1. 在 [選取]  文字方塊中，鍵入您要新增的使用者名稱或電子郵件。
1. 選取使用者。
1. 選取 [ **儲存**]。
1. 在幾分鐘之後，即會在訂用帳戶範圍將「帳單讀者」角色指派給使用者。
1. 帳單讀者會收到一封電子郵件，其中包含用來登入的連結。

    ![顯示「帳單讀者」在 Azure 入口網站中可見內容的螢幕擷取畫面](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>允許部門系統管理員或帳單擁有者存取帳單

企業系統管理員可以允許部門系統管理員和帳戶擁有者檢視與所管理部門和帳戶相關聯的使用量詳細資料和成本。

1. 以企業系統管理員身分登入 [EA 入口網站](https://ea.azure.com/)。
1. 選取 [管理]  。
1. 在 [註冊]  下，將 [DA 檢視費用]  變更為 [啟用]  ，讓部門系統管理員檢視使用量和成本。
1. 將 [AO 檢視費用]  變更為 [啟用]  ，讓帳戶擁有者檢視使用量和成本。


如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](billing-understand-ea-roles.md)。

## <a name="only-account-admins-can-access-account-center"></a>只有帳戶管理員才能存取帳戶中心

「帳戶管理員」是訂用帳戶的法定擁有者。 註冊或購買 Azure 訂用帳戶的人員預設即是「帳戶管理員」，除非[已將訂用帳戶擁有權轉移](billing-subscription-transfer.md)給其他人。 帳戶管理員可以從[帳戶中心](https://account.azure.com/Subscriptions)建立訂用帳戶、取消訂用帳戶、變更訂用帳戶的帳單地址，以及管理訂用帳戶的存取原則。

## <a name="next-steps"></a>後續步驟

- 具備其他角色 (例如「擁有者」和「參與者」) 的使用者不僅可存取帳單資訊，還可存取 Azure 服務。 若要管理這些角色，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。
- 如需角色的詳細資訊，請參閱 [Azure 資源的內建角色](../role-based-access-control/built-in-roles.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
