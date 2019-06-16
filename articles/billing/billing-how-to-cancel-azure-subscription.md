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
ms.openlocfilehash: 8f4279d9ac085cdd1ded0dfdda4fad9d3fe12fb8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480229"
---
# <a name="cancel-your-subscription-for-azure"></a>取消您的 Azure 訂用帳戶

僅有 Azure 訂用帳戶[帳戶系統管理員](billing-subscription-transfer.md#whoisaa)可以取消 Azure 訂用帳戶。 Azure 訂用帳戶系統管理員也可以[指派訂用帳戶系統管理員身分的另一位使用者](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription)，讓他們可以取消訂用帳戶。 取消訂用帳戶之後，您將無法存取 Azure 服務和資源端點。

取消訂用帳戶之前︰

* 備份您的資料。 例如，如果您將資料儲存在 Azure 儲存體或 SQL，請下載複本。 如果您有虛擬機器，請將其映像儲存在本機。
* 關閉您的服務。 移至[管理入口網站中的資源頁面](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，並**停止**任何執行中的虛擬機器、應用程式或其他服務。
* 考慮移轉您的資料。 請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。
* 您必須刪除所有資源和所有的資源群組。 需要刪除它們，才可以取消訂用帳戶。 必須個別刪除每個資源群組。 期間刪除資源群組，您必須輸入資源群組名稱，以確認刪除。
* 如果您有參考此訂用帳戶的任何自訂角色`AssignableScopes`，您應該更新這些自訂的角色，以移除訂用帳戶。 如果您嘗試更新自訂角色，在取消訂用帳戶之後，您可能會發生錯誤。 如需詳細資訊，請參閱 <<c0> [ 疑難排解問題的自訂角色](../role-based-access-control/troubleshooting.md#problems-with-custom-roles)並[適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)。

如果您取消付費 Azure 支援方案，剩餘的訂閱期間仍會計費。 如需詳細資訊，請參閱 [Azure 支援方案](https://azure.microsoft.com/support/plans/)。

## <a name="cancel-subscription-using-the-azure-portal"></a>使用 Azure 入口網站取消訂用帳戶

1. 在 [Azure 入口網站的 [訂用帳戶] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中選取您的訂用帳戶。
2. 選取您要取消的訂用帳戶。
3. 選取[概觀]  ，然後選取 [取消訂用帳戶]  。

    ![顯示 [取消] 按鈕的螢幕擷取畫面](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. 遵循提示並完成取消作業。

## <a name="what-happens-after-i-cancel-my-subscription"></a>取消訂用帳戶之後會發生什麼情況？

取消之後，就會立即停止計費。 不過，最多可能需要 10 分鐘，取消作業才會在入口網站中顯示。 如果您在計費期間中途取消訂用帳戶，我們會在該期間結束後的一般發票核發日傳送最後一張帳單。

取消之後，會停用您的服務。 這表示系統會解除配置您的虛擬機器、釋出暫時 IP 位址，且儲存體會變成唯讀。

我們會在 90 天後才永久刪除您的資料，以防您需要存取該資料或改變心意。 我們不會向您收取保留資料的費用。 若要深入了解，請參閱 [Microsoft 信任中心 - 我們如何管理您的資料](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) \(英文\)。

## <a name="reactivate-subscription"></a>重新啟動訂用帳戶

如果您不小心取消隨用隨付訂用帳戶，您可以[在帳戶中心重新啟動它](billing-subscription-become-disable.md)。

如果您的訂用帳戶不是隨用隨付類型，請在取消後 90 天內與支援服務連絡，以重新啟動您的訂用帳戶。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
