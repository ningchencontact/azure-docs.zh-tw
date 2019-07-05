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
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491092"
---
# <a name="manage-access-to-billing-information-for-azure"></a>管理對 Azure 帳單資訊的存取

您可以提供其他人存取帳單資訊為您在 Azure 入口網站中的帳戶。 計費的角色類型以及提供存取帳單資訊的指示取決於您的帳單帳戶類型。 若要判斷您的帳單帳戶類型，請參閱[檢查您的帳單帳戶類型](#check-the-type-of-your-billing-account)。

本文適用於具有 Microsoft Online Service 程式帳戶的客戶。 如果您是使用 Enterprise 合約 (EA) 的 Azure 客戶，而且是企業系統管理員，則您可以在企業版入口網站來提供給部門系統管理員和帳戶擁有者權限。 如需詳細資訊，請參閱[了解 Azure 中的 Azure Enterprise 合約系統管理角色](billing-understand-ea-roles.md)。 如果您是 Microsoft 客戶合約的客戶，請參閱，[了解 Microsoft 客戶合約在 Azure 中的系統管理角色](billing-understand-mca-roles.md)。 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>程式的 Microsoft 線上服務帳戶的帳戶系統管理員

帳戶管理員是唯一的擁有者，Microsoft 線上服務方案計費的帳戶。 角色會指派給已註冊 Azure 的人員。 帳戶系統管理員有權執行各種計費工作，例如建立訂用帳戶、 檢視發票或變更訂用帳戶的計費。

## <a name="give-others-access-to-view-billing-information"></a>讓其他人存取權可以檢視帳單資訊

帳戶系統管理員可以授與其他 Azure 的計費資訊存取權指派下列角色的訂用帳戶，其帳戶中的其中一個。

- 服務管理員
- 共同管理員
- 擁有者
- 參與者
- 讀取者
- 帳單讀取器

這些角色具有在計費資訊存取權[Azure 入口網站](https://portal.azure.com/)。 指派這些角色的人員也可以使用[計費 Api](billing-usage-rate-card-overview.md)以程式設計方式取得發票和使用方式詳細資料。

若要指派角色，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。

\* * 如果您是 EA 客戶，帳戶擁有者可以將上述的角色指派給小組的其他使用者。 但是這些使用者可以檢視帳單資訊中，企業系統管理員必須啟用企業版入口網站中的 AO 檢視費用。


### <a name="opt-in"></a> 允許使用者下載發票

帳戶管理員已將適當的角色指派給其他使用者之後，必須在存取以下載發票，Azure 入口網站中的開啟。 2016 年 12 月以前的發票僅供帳戶管理員使用。

1. 登入[Azure 入口網站](https://portal.azure.com/)，做為帳戶管理員，

1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. 選取 **訂用帳戶**左側窗格中。 根據您的存取權，您可能需要選取計費的範圍，然後選取**訂用帳戶**。
 
    ![螢幕擷取畫面顯示選取的訂用帳戶](./media/billing-manage-access/billing-select-subscriptions.png)

1. 選取 **發票**，然後**發票的存取權**。

    ![螢幕擷取畫面顯示如何委派存取發票](./media/billing-manage-access/AA-optin.png)

1. 選取 [開啟]  ，然後儲存。

    ![螢幕擷取畫面顯示委派存取發票的開關](./media/billing-manage-access/AA-optinAllow.png)

「帳戶管理員」也可以設定透過電子郵件傳送發票。 若要深入了解，請參閱[透過電子郵件取得發票](billing-download-azure-invoice-daily-usage-date.md)。

## <a name="give-read-only-access-to-billing"></a>授與帳單唯讀存取權

請將「帳單讀者」角色指派給需要以唯讀方式存取訂用帳戶帳單資訊，但不需要能夠管理或建立 Azure 服務的使用者。 此角色適用於組織內負責 Azure 訂用帳戶之財務和成本管理的使用者。

「帳單讀者」功能目前為預覽版，尚未支援非全球雲端。

1. 登入[Azure 入口網站](https://portal.azure.com/)，做為帳戶管理員，

1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. 選取 **訂用帳戶**左側窗格中。 根據您的存取權，您可能需要選取計費的範圍，然後選取**訂用帳戶**。
 
    ![螢幕擷取畫面顯示選取的訂用帳戶](./media/billing-manage-access/billing-select-subscriptions.png)

1. 選取 [存取控制 (IAM)]  。
1. 選取 **新增**從頁面頂端。

    ![螢幕擷取畫面顯示按一下 新增角色指派](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. 在 [角色]  下拉式清單中，選擇 [帳單讀者]  。
1. 在 [選取]  文字方塊中，鍵入您要新增的使用者名稱或電子郵件。
1. 選取使用者。
1. 選取 [ **儲存**]。
    ![螢幕擷取畫面顯示按一下 新增角色指派](./media/billing-manage-access/billing-save-role-assignment.png)

1. 幾分鐘之後, 使用者已指派訂用帳戶帳單讀者 」 角色。

\* * 如果您是 EA 客戶，帳戶擁有者或部門系統管理員可以指派 「 帳單讀者 」 角色給小組成員。 但若要讓帳單讀者檢視部門或帳戶的帳單資訊，企業系統管理員必須在企業版入口網站中啟用 [AO 檢視費用]  或 [DA 檢視費用]  原則。

## <a name="check-the-type-of-your-billing-account"></a>請檢查您的帳單帳戶類型
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>後續步驟

- 具備其他角色 (例如「擁有者」和「參與者」) 的使用者不僅可存取帳單資訊，還可存取 Azure 服務。 若要管理這些角色，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。
- 如需角色的詳細資訊，請參閱 [Azure 資源的內建角色](../role-based-access-control/built-in-roles.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
