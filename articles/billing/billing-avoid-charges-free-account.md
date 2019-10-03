---
title: 避免 Azure 免費帳戶產生費用
description: 了解為什麼您看到 Azure 免費帳戶的費用。 了解如何避免這些費用。
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 3fcd2bed0ea6a3ad4f2dba36113bf33461b51ce6
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709728"
---
# <a name="avoid-charges-with-your-azure-free-account"></a>避免 Azure 免費帳戶產生費用

Azure 免費帳戶提供可在前 30 天內使用的 Azure 點數美金 200 元，以及 12 個月數量有限的免費服務。 如需詳細資訊，請參閱 [Azure 免費帳戶](https://azure.microsoft.com/free/)。 依據您點數的狀態而定，您可以使用點數，或對於免費服務與數量以外的使用量付費。

## <a name="azure-free-account-might-use-account-credit"></a>Azure 免費帳戶可能會使用帳戶額度
如果您仍有尚未到期的 Azure 點數，Azure 會使用點數來支付免費服務和數量以外的使用量。

## <a name="your-credit-runs-out-or-is-expired"></a>您的額度已用完或已到期
如果額度用完或在 30 天結束時過期，Azure 會停用您的訂用帳戶。 若要繼續使用 Azure 服務，您必須將訂用帳戶升級為採用隨用隨付費率的個別訂用帳戶。 如需詳細資訊，請參閱[將免費試用 Azure 訂用帳戶升級](billing-upgrade-azure-subscription.md)。 升級之後，您的訂用帳戶仍可存取免費服務 12 個月。 您只要支付免費服務和數量以外的使用量。

讓我們看看 Azure 免費帳戶產生費用的一些原因。

### <a name="usage-exceeds-the-limits-of-free-services"></a>使用量超過免費服務的限額

Azure 免費帳戶提供使用量有限的每個月免費服務。 免費使用量會在月底到期，不會累計至下個月。 例如，您每個月有 5 GB 的檔案儲存體使用量。 如果，您在一個月中只使用 2 GB，剩餘的 3 GB 不會累計至下個月。 若要避免產生費用，請在使用量維持在限額內。 若要了解免費服務的限額，請參閱 [Azure 免費帳戶常見問題集](https://azure.microsoft.com/free/free-account-faq/)。 若要檢查您的免費服務使用量，請參閱[檢查 Azure 免費帳戶提供的免費服務使用量](billing-check-free-service-usage.md)。

### <a name="some-services-are-not-free"></a>有些服務不是免費的

若使用並非 Azure 免費帳戶隨附的免費服務，則會以隨用隨付費率向您收費。 若要了解免費帳戶隨附的服務，請參閱 [Azure 免費帳戶常見問題集](https://azure.microsoft.com/free/free-account-faq/)。 您可以在 Azure 入口網站或 Azure 使用量檔案中檢查您的服務使用量。 若要進一步了解，請參閱[定期查看入口網站以了解成本細分和完工速率](billing-getting-started.md#costs)和[從帳戶中心下載使用量](billing-download-azure-invoice-daily-usage-date.md)。

### <a name="you-reached-the-end-of-your-free-12-months"></a>您已達 12 個月的免費使用期

您的免費服務與數量會在 12 個月結束時到期。 您可以在 Azure 入口網站查到免費服務的到期日。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側導覽區域中，選取 [所有服務]  。

3.  選取 **訂用帳戶** 。

4.  選取您註冊免費帳戶時建立的訂用帳戶。

5.  向下捲動以尋找免費服務方格。 按一下方格左上方的工具提示。

![顯示可在哪裡找到免費帳戶權益何時到期的螢幕擷取畫面](./media/billing-avoid-charges-free-account/freeaccount-benefits-expiration-date.png)


您的免費服務與數量到期之後，Azure 會針對您使用的任何服務收取隨用隨付費率。 您可以使用 Azure 入口網站，對於不使用的服務刪除資源。 如果您不想要使用任何 Azure 服務，可以[取消訂閱](billing-how-to-cancel-azure-subscription.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- [升級您的免費試用 Azure 訂用帳戶](billing-upgrade-azure-subscription.md)
