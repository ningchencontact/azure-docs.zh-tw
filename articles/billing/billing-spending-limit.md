---
title: Azure 消費限制 |Microsoft Docs
description: 本文說明 Azure 消費限制的運作方式，以及如何將它移除。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490604"
---
# <a name="azure-spending-limit"></a>Azure 消費限制

在 Azure 中的消費限制可避免花費超過點數額度。 所有新客戶只要註冊 Azure 試用版或包含多月的信用額度的供應項目有預設開啟消費限制。 消費限制是 $0。 此限制無法變更。 針對這類的承諾用量方案和隨用隨付定價計劃，計劃下一些訂用帳戶，無法使用消費限制。 請參閱[完整的 Azure 供應項目及消費限制可用性清單](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="reaching-a-spending-limit"></a>達到消費限制

在完每月金額，隨附於您 Azure 訂用帳戶，當您部署的服務會停用該計費週期的其餘部分。

例如，若您花光您的訂用帳戶隨附的所有信用額度，您部署的 Azure 資源會從生產環境和 Azure 虛擬機器會停止並解除配置。 儲存體帳戶中的資料會以唯讀狀態。

在下一個計費週期開始時，如果您的訂用帳戶優惠包含多個月，點數您訂用帳戶重新啟用自動。 然後您可以重新部署您的 Azure 資源，並讓您的儲存體帳戶和資料庫的完整存取。

Azure 會傳送電子郵件通知，當您達到消費限制的訂用帳戶。 登入[帳戶中心](https://account.windowsazure.com/Subscriptions)若要查看有關已達消費限制的訂用帳戶的通知。

如果您有免費的試用版訂用帳戶，並達到消費限制，您可以升級至與計劃[隨用隨付](billing-upgrade-azure-subscription.md)定價，從而移除消費限制，並自動啟用訂用帳戶。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>在帳戶中心裡移除消費限制

只要您的 Azure 訂用帳戶相關聯的有效的付款方法，您可以移除消費限制，在任何時間。 針對具有多月點數的優惠，您也可以啟用消費限制在您的下一個計費週期的開頭。

若要移除您的消費限制，請依照下列步驟操作：

1. 登入[帳戶中心](https://account.windowsazure.com/Subscriptions)。
1. 選取一個訂用帳戶。 如果由於達到消費限制，訂用帳戶已停用，請按一下 通知：**訂用帳戶達到消費限制，並已停用以避免收費。** 否則，請按一下 [訂用帳戶狀態]  區域中的 [移除消費限制]  。
1. 選取適合您的選項。

![選取用於移除消費限制的選項](./media/billing-spending-limit/remove-spending-limit.PNG)

| 選項 | 效果 |
| --- | --- |
| 無限期移除消費限制 | 移除消費限制，不在下一個計費週期開始時自動開啟。 |
| 移除目前計費週期的消費限制 | 移除消費限制，讓它在下一個計費週期開始時自動開啟。 |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>為什麼您可能想要移除消費限制

消費限制會使您無法部署或使用某些協力廠商和 Microsoft 的服務。 以下是您應該在您的訂用帳戶移除消費限制的情況。

-  您打算部署第一方映像 (例如 Oracle) 和服務 (例如 Azure DevOps Services)。 這種情況會讓您幾乎立即超過消費限制，並讓您的訂用帳戶停用。
- 您有不想中斷的服務。
- 您的服務及資源有您不想遺失的設定 (例如虛擬 IP 位址)。 這些設定在服務和資源解除配置時遺失。

## <a name="turn-on-the-spending-limit-after-removing"></a>開啟 移除消費限制之後

唯有無限期移除消費限制時，才能使用這項功能。 請將它變更為在下一個計費週期開始時自動開啟。

1. 登入[帳戶中心](https://account.windowsazure.com/Subscriptions)。
1. 按一下黃色橫幅以變更消費限制選項。
1. 選擇 [在下一個計費週期 \<計費週期開始日期\> 啟用消費限制] 

## <a name="custom-spending-limit"></a>自訂消費限制

無法使用自訂消費限制。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>消費限制不會防止所有費用

[Azure Marketplace 中發佈的某些外部服務](billing-understand-your-azure-marketplace-charges.md)不能與您的訂用帳戶信用額度，並會產生個別的費用，即使已設定您的消費限制。 範例包括 Visual Studio 授權、Azure Active Directory Premium、支援方案，以及大部分的第三方品牌服務。 當您佈建新的外部服務時，系統會顯示警告，讓您知道服務會另外收費︰

![Marketplace 購買警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 升級至與計劃[隨用隨付](billing-upgrade-azure-subscription.md)定價。
