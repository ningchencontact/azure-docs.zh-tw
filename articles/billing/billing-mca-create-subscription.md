---
title: 建立為帳單帳戶的其他 Azure 訂用帳戶 |Microsoft Docs
description: 了解如何在 Azure 入口網站中新增 Azure 訂用帳戶。
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 105b8481486c088a05e3acb95081d3ee55b55f52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371975"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Microsoft 客戶協議建立額外的 Azure 訂用帳戶

建立額外的訂用帳戶為帳單帳戶設定個別環境進行開發和測試，就會有安全性或隔離資料合規性原因。

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。 如果您想要建立其他計費帳戶的訂用帳戶，請參閱[在 Azure 入口網站中建立的其他訂用帳戶](billing-create-subscription.md)。

若要建立訂用帳戶，您必須是**發票區段擁有者**，**發票區段參與者**，或**Azure 訂用帳戶的建立者**。 如需詳細資訊，請參閱 <<c0> [ 訂用帳戶計費的角色和工作](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks)。 若要提供其他人建立為帳單帳戶的 Azure 訂用帳戶的權限，請參閱[讓其他人建立 Azure 訂用帳戶的權限](#give-others-permission-to-create-azure-subscriptions)。

## <a name="create-a-subscription-in-the-azure-portal"></a>在 Azure 入口網站中建立訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 尋找**訂用帳戶**。

   ![在訂用帳戶的入口網站中顯示搜尋螢幕擷取畫面](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 選取 [新增]

4. 如果您有多個計費帳戶的存取權，請選取您的 Microsoft 客戶合約的計費帳戶。

   ![示範建立訂用帳戶 頁面的螢幕擷取畫面](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. 選取計費的設定檔。 您的訂用帳戶費用將會反映在帳單設定檔的發票上，並會使用其付款方式付費。 如果您有只有一個帳單寄送的設定檔存取權時，選取項目會呈現灰色。

6. 選取 [發票] 區段。 計費的設定檔的發票的這個區段，將會反映您訂用帳戶的費用。 如果您需要存取只能有一個 「 發票 」 區段，選取項目會呈現灰色。

7. 選取的訂用帳戶計劃。 選取  **Microsoft Azure 研發/測試計劃**，如果您打算使用此訂用帳戶進行開發，或使用其他的測試工作負載**Microsoft Azure 方案**。 如果您有存取權只能有一個計劃時，選取項目會呈現灰色。

8. 輸入訂用帳戶名稱。 此名稱有助於您輕鬆地識別在 Azure 入口網站中的訂用帳戶。

9. 選取 [建立] 。

## <a name="give-others-permission-to-create-azure-subscriptions"></a>讓其他人建立 Azure 訂用帳戶的權限

將使用者新增為 [發票] 區段上提供這些權限來建立 Azure 訂用帳戶的 Azure 訂用帳戶建立者。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單] 進行搜尋。

   ![在訂用帳戶的入口網站中顯示搜尋螢幕擷取畫面](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 移至 [發票] 區段。 根據您的存取權，您可能需要選取 帳單帳戶或計費的設定檔。 從計費的帳戶或設定檔上，選取**發票區段**，然後發票 > 一節。

4. 選取 **存取管理 (IAM)** 從左上方。

5. 從頁面頂端選取 [新增]。

6. 選取  **Azure 訂用帳戶的建立者**角色。

   ![若要讓 Azure 訂用帳戶建立者角色會顯示的螢幕擷取畫面](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. 輸入您要授與存取權之使用者的電子郵件地址。

8. 選取 [ **儲存**]。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [讓其他人使用內建角色建立 Azure 資源的權限](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [建立 windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md)
- [建立 linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md)
- [建立用於資源組織及管理的管理群組](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
