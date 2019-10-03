---
title: 為您的計費帳戶建立額外的 Azure 訂用帳戶
description: 了解如何在 Azure 入口網站中新增 Azure 訂用帳戶。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e1fafafe5f67d1775ca80a1f7c2aff4dec9e0bc4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709579"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>為 Microsoft 客戶合約建立額外的 Azure 訂用帳戶

為您的計費帳戶建立額外的訂用帳戶以設定個別環境，用於開發、測試、安全性等用途，或基於相容性因素隔離資料。

本文適用於 Microsoft 客戶合約的計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-access)。 如果您想要為其他類型的計費帳戶建立訂用帳戶，請參閱[在 Azure 入口網站中建立額外的訂用帳戶](billing-create-subscription.md)。

若要建立訂用帳戶，您必須是**發票區段擁有者**、**發票區段參與者**或 **Azure訂用帳戶建立者**。 如需詳細資訊，請參閱[訂用帳戶計費角色和工作](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks)。 若要授與他人為計費帳戶建立 Azure 訂用帳戶的權限，請參閱[授與他人建立 Azure 訂用帳戶的權限](#give-others-permission)。

## <a name="create-a-subscription"></a>建立訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 尋找**訂用帳戶**。

   ![顯示在入口網站中搜尋訂用帳戶的螢幕擷取畫面](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. 選取 [新增] 

4. 如果您有多個計費帳戶的存取權，請選取您 Microsoft 客戶合約的計費帳戶。

   ![顯示 [建立訂用帳戶] 頁面的螢幕擷取畫面](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. 選取帳單設定檔。 訂用帳戶的費用將會計入選取的帳單設定檔。 如果您只能存取一個帳單設定檔，選取項目將會呈現為灰色。

6. 選取發票區段。 訂用帳戶的費用將會計入帳單設定檔的這個發票區段。 如果您只能存取一個發票區段，選取項目將會呈現為灰色。

7. 選取訂用帳戶的方案。 如果您打算將此訂用帳戶用於開發或測試工作負載，請選取 [適用於 DevTest 的 Microsoft Azure 方案]  ，否則請使用 [Microsoft Azure 方案]  。 如果您只能存取一個方案，選取項目將會呈現為灰色。

8. 輸入訂用帳戶名稱。 此名稱可協助您輕鬆識別 Azure 入口網站中的訂用帳戶。

9. 選取 [建立]  。

## <a name="give-others-permission"></a>為其他人授與權限

在發票區段上將使用者新增為 Azure 訂用帳戶建立者，為他們授與建立 Azure 訂用帳戶的權限。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在入口網站中搜尋訂用帳戶的螢幕擷取畫面](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 移至 [發票] 區段。 視存取權之不同，您可能必須選取計費帳戶或帳單設定檔。 請從計費帳戶或帳單設定檔中選取 [發票區段]  ，然後從清單中選取發票區段。 使用者所將建立的任何訂用帳戶，費用都會計入此發票區段。
   
   ![顯示選取發票區段的螢幕擷取畫面](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. 從左上方選取 [存取管理 (IAM)]  。

5. 從頁面頂端選取 [新增]  。

6. 選取 [Azure 訂用帳戶建立者]  作為角色。

7. 輸入您要為其授與存取權之使用者的電子郵件地址。

8. 選取 [儲存]  。

## <a name="check-access"></a>檢查存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [授與他人使用內建角色建立 Azure 資源的權限](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)
- [建立 Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)
- [建立用於資源組織及管理的管理群組](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
