---
title: Azure 消費限制 | Microsoft Docs
description: 本文說明 Azure 消費限制的運作方式及如何移除此限制。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114441"
---
# <a name="azure-spending-limit"></a>Azure 消費限制

Azure 消費限制可避免您的花費超過您的點數額度。 根據預設，系統會針對所有註冊 Azure 試用版或包含多月點數之供應項目的新客戶開啟消費限制。 消費限制為 $0，且無法變更。 例如，您無法將消費限制變更為 $100。 不過，您可以移除消費限制。 因此，您可能沒有任何限制，或限制為零，使您無法進行大部分的消費。 消費限制不適用於某些方案 (例如承諾用量方案和採用隨用隨付定價的方案) 之下的訂用帳戶。 請參閱[完整的 Azure 供應項目及消費限制可用性清單](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="reaching-a-spending-limit"></a>達到消費限制

若您用完 Azure 訂用帳戶隨附的每月額度，則您部署的服務在該計費期間的剩餘時間內將被停用。

舉例來說，若您花光訂用帳戶隨附的所有點數，您部署的 Azure 資源便會從生產環境中移除，且 Azure 虛擬機器會停止運作並解除配置。 您儲存體帳戶中的資料只能以唯讀方式提供。

下一個計費期間開始時，若您的訂用帳戶供應項目包含多月點數，系統便會自動重新啟用您的訂用帳戶。 接著，您便可以重新部署 Azure 資源，並完整存取您的儲存體帳戶和資料庫。

當您達到訂用帳戶的消費限制時，Azure 會傳送電子郵件通知給您。 登入[帳戶中心](https://account.windowsazure.com/Subscriptions)，以查看訂用帳戶達到消費限制的通知。

如果您有免費試用訂用帳戶並達到消費限制，可以升級為採用[隨用隨付](billing-upgrade-azure-subscription.md)定價的方案來移除消費限制並自動啟用此訂用帳戶。

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>在帳戶中心裡移除消費限制

只要您的 Azure 訂用帳戶所關聯的付款方式有效，您就可以隨時移除消費限制。 針對有多月點數的供應項目，您也可以在下一個計費期間開始時啟用消費限制。

若要移除您的消費限制，請依照下列步驟操作：

1. 登入[帳戶中心](https://account.windowsazure.com/Subscriptions)。
1. 選取一個訂用帳戶。 如果由於達到消費限制而停用訂用帳戶，請按一下此通知：**此訂用帳戶已達消費限制，並已停用以避免收費。** 否則，請按一下 [訂用帳戶狀態]  區域中的 [移除消費限制]  。
1. 選取適合您的選項。

![選取用於移除消費限制的選項](./media/billing-spending-limit/remove-spending-limit.PNG)

| 選項 | 效果 |
| --- | --- |
| 無限期移除消費限制 | 移除消費限制，不在下一個計費週期開始時自動開啟。 |
| 移除目前計費週期的消費限制 | 移除消費限制，讓它在下一個計費週期開始時自動開啟。 |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>您為何要移除消費限制

消費限制會使您無法部署或使用某些協力廠商和 Microsoft 的服務。 以下是您應該移除訂用帳戶消費限制的情況。

-  您打算部署第一方映像 (例如 Oracle) 和服務 (例如 Azure DevOps Services)。 此情況會讓您幾乎立即超過消費限制，而導致訂用帳戶遭停用。
- 您有不想要中斷的服務。
- 您的服務及資源有您不想遺失的設定 (例如虛擬 IP 位址)。 這些設定在服務和資源解除配置時遺失。

## <a name="turn-on-the-spending-limit-after-removing"></a>在移除消費限制之後再將其開啟

唯有無限期移除消費限制時，才能使用這項功能。 請將它變更為在下一個計費週期開始時自動開啟。

1. 登入[帳戶中心](https://account.windowsazure.com/Subscriptions)。
1. 按一下黃色橫幅以變更消費限制選項。
1. 選擇 [在下一個計費週期 \<計費週期開始日期\> 啟用消費限制] 

## <a name="custom-spending-limit"></a>自訂消費限制

無法使用自訂消費限制。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>消費限制不會防止所有費用

[某些在 Azure Marketplace 中發佈的外部服務](billing-understand-your-azure-marketplace-charges.md)無法透過訂用帳戶點數來使用，並會產生另外的費用，即使您已設定消費限制亦然。 範例包括 Visual Studio 授權、Azure Active Directory Premium、支援方案，以及大部分的第三方品牌服務。 當您佈建新的外部服務時，系統會顯示警告，讓您知道服務會另外收費︰

![Marketplace 購買警告](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 升級為採用[隨用隨付](billing-upgrade-azure-subscription.md)定價的方案。
