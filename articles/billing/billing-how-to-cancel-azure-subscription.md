---
title: 取消 Azure 訂用帳戶 | Microsoft Docs
description: 說明如何取消 Azure 訂用帳戶，例如免費試用訂用帳戶
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666437"
---
# <a name="cancel-your-azure-subscription"></a>取消 Azure 訂用帳戶

如果您不再需要訂用帳戶, 您可以在 Azure 入口網站中取消您的 Azure 訂用帳戶。 

取消訂用帳戶之前︰
* 備份您的資料。 例如，如果您將資料儲存在 Azure 儲存體或 SQL，請下載複本。 如果您有虛擬機器，請將其映像儲存在本機。
* 關閉您的服務。 移至[管理入口網站中的資源頁面](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，並**停止**任何執行中的虛擬機器、應用程式或其他服務。
* 考慮移轉您的資料。 請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。
* 刪除所有資源和所有資源群組。 您必須先刪除這些專案, 才能取消訂用帳戶。 每個資源群組都必須個別刪除。 刪除資源群組時, 您必須輸入資源組名來確認刪除。
* 如果您在中有任何參考此訂用帳戶`AssignableScopes`的自訂角色, 您應該更新這些自訂角色以移除訂用帳戶。 如果您嘗試在取消訂用帳戶之後更新自訂角色, 可能會收到錯誤。 如需詳細資訊, 請參閱[針對 Azure 資源](../role-based-access-control/custom-roles.md)的自訂角色和自訂角色的[問題進行疑難排解](../role-based-access-control/troubleshooting.md#problems-with-custom-roles)。

如果您取消付費 Azure 支援方案, 則會向您收取其他訂用帳戶期限的費用。 如需詳細資訊，請參閱 [Azure 支援方案](https://azure.microsoft.com/support/plans/)。

## <a name="cancel-subscription-in-the-azure-portal"></a>取消 Azure 入口網站中的訂用帳戶

1. 從 [Azure 入口網站的訂用帳戶][頁面中](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)選取您的訂閱。
2. 選取您要取消的訂用帳戶。
3. 選取[概觀]，然後選取 [取消訂用帳戶]。
    ![顯示 [取消] 按鈕的螢幕擷取畫面](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. 遵循提示並完成取消作業。


## <a name="who-can-cancel-a-subscription"></a>誰可以取消訂用帳戶？

下表描述取消訂用帳戶所需的許可權。

|訂閱類型     |誰可以取消  |
|---------|---------|
|當您透過 Azure 網站註冊 Azure 時所建立的訂用帳戶。 例如, 當您註冊[Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)時, 會[使用隨用隨付費率](https://azure.microsoft.com/offers/ms-azr-0003p/)或[Visual studio 訂閱者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)的帳戶來登入。 |  訂用帳戶的帳戶管理員、擁有者和參與者  |
|[Microsoft Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/)與[企業開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  訂閱的帳戶擁有者、擁有者和參與者       |
|[適用于 DevTest 的](https://azure.microsoft.com/offers/ms-azr-0148g/) [Azure 方案](https://azure.microsoft.com/offers/ms-azr-0017g/)和 azure 方案     |  訂用帳戶的擁有者和參與者      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>取消訂用帳戶之後會發生什麼情況？

取消之後, 就會立即停止計費。 不過，最多可能需要 10 分鐘，取消作業才會在入口網站中顯示。 如果您在計費期間中途取消, 我們會在該期間結束後, 傳送一般發票日期的最後發票。

取消之後, 即會停用您的服務。 這表示系統會解除配置您的虛擬機器、釋出暫時 IP 位址，且儲存體會變成唯讀。

我們會在 90 天後才永久刪除您的資料，以防您需要存取該資料或改變心意。 我們不會向您收取保留資料的費用。 若要深入了解，請參閱 [Microsoft 信任中心 - 我們如何管理您的資料](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) \(英文\)。

## <a name="reactivate-subscription"></a>重新啟動訂用帳戶

如果您不小心以隨用隨付費率取消訂用帳戶, 您可以[在帳戶中心重新啟用它](billing-subscription-become-disable.md)。

如果您的訂用帳戶不是隨用隨付費率的訂用帳戶, 請在取消後的90天內聯絡支援以重新啟用您的訂用帳戶。

## <a name="need-help-contact-us"></a>需要協助嗎? 與我們連絡。

如果您有任何疑問或需要協助, 請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
