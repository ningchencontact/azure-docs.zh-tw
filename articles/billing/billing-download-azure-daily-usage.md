---
title: 檢視及下載 Azure 使用量和費用
description: 說明如何下載或檢視您的 Azure 每日使用量和費用資訊。
keywords: 計費使用量,使用量費用, 使用量下載, 檢視使用量, azure 發票,azure 使用量
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 23cd7c3765fc99eb5907aa853d7431d5e247aea6
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709717"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>檢視及下載您的 Azure 使用量和費用

如果您是 EA 客戶或擁有 [Microsoft 客戶合約](#check-your-access-to-a-microsoft-customer-agreement)，您可以在 [Azure 入口網站](https://portal.azure.com/)中下載 Azure 使用量和費用。 針對其他訂用帳戶，請前往 [Azure 帳戶中心](https://account.azure.com/Subscriptions)下載使用量。

只有特定角色有權取得 Azure 使用量資訊，例如帳戶管理員或企業系統管理員。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](billing-manage-access.md)。

如果您有 [Microsoft 客戶合約](#check-your-access-to-a-microsoft-customer-agreement)，您必須是帳單設定檔擁有者、參與者、讀者或發票管理員，才能檢視 Azure 使用量和費用資訊。 若要深入了解 Microsoft 客戶合約的計費角色，請參閱[帳單設定檔角色和工作](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="download-usage-from-the-account-center-csv"></a>從帳戶中心下載使用量 (.csv)

1. 以帳戶管理員身分登入 [Azure 帳戶中心](https://account.windowsazure.com/subscriptions)。

2. 選取您需要發票和使用資訊的訂用帳戶。

3. 選取 [帳單記錄]  。

    ![顯示帳單記錄選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 您可以查看過去六個計費期間和目前未收帳期間的對帳單。

    ![顯示計費期間、下載發票和每日使用量的選項，以及每個計費期間總費用的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 選取 [檢視目前的對帳單]  ，以查看產生估計時的估計費用。 此資訊每天只會更新一次，可能不會包含您的所有使用量。 您的每月發票可能會與這項估計有所不同。

    ![顯示 [檢視目前對帳單] 選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![顯示目前費用預估的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. 選取 [下載使用量]  ，以 CSV 檔案形式下載每日使用量資料。 如果您看到兩個可用版本，請下載第 2 個版本。

    ![顯示 [下載使用量] 選項的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

只有帳戶管理員可以存取 Azure 帳戶中心。 其他計費系統管理員 (例如擁有者) 可以使用 [計費 API](billing-usage-rate-card-overview.md)取得使用量資訊。

如需每日使用量的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。 如需管理成本的說明，請參閱[使用 Azure 計費與成本管理避免非預期的成本](billing-getting-started.md)。

## <a name="download-usage-for-ea-customers"></a>下載 EA 客戶的使用量

若要檢視及下載 EA 客戶的使用量資料，您必須是企業系統管理員、帳戶擁有者或部門系統管理員，並且已啟用檢視費用原則。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. 選取 [使用量 + 費用]  。
1. 針對您想要下載的月份，選取 [下載]  。

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>下載 Microsoft 客戶合約的使用量

如果您有 Microsoft 客戶合約，則可以下載帳單設定檔的 Azure 使用量和費用資訊。 您必須是帳單設定檔擁有者、參與者、讀者或發票管理員，才能下載 Azure 使用量和費用 CSV。

### <a name="download-usage-for-billed-charges"></a>下載計費費用的使用量

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 搜尋 [成本管理 + 帳單]  。
3. 選取帳單設定檔。 視存取權之不同，您可能必須先選取計費帳戶。
4. 選取 [發票]  。
5. 在發票方格中，尋找對應至所要下載使用量的發票資料列。
6. 按一下資料列結尾處的省略符號 (`...`)。

    ![在資料列結尾處顯示省略符號的螢幕擷取畫面](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. 在下載操作功能表中，選取 [Azure 使用量和費用]  。

     ![顯示已選取 Azure 使用量和費用的螢幕擷取畫面](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>下載待決費用的使用量

您也可以下載目前計費期間的當月使用量。 這些使用量費用尚未計費。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 搜尋 [成本管理 + 帳單]  。
3. 選取帳單設定檔。 視存取權之不同，您可能必須先選取計費帳戶。
4. 在 [概觀]  區域中，尋找位於當月費用下方的下載連結。
5. 選取 [Azure 使用量和費用]  。

    ![顯示從概觀下載的螢幕擷取畫面](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>檢查您對 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您的發票和使用量費用，請參閱：

- [了解您 Microsoft Azure 詳細使用量上的字詞](billing-understand-your-usage.md)
- [了解 Microsoft Azure 帳單](billing-understand-your-bill.md)
- [檢視及下載您的 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [檢視及下載組織的 Azure 定價](billing-ea-pricing.md)

如果您有 Microsoft 客戶合約，請參閱：

- [了解 Microsoft 客戶合約 Azure 詳細使用量的詞彙](billing-mca-understand-your-usage.md)
- [了解 Microsoft 客戶合約發票上的費用](billing-mca-understand-your-bill.md)
- [檢視及下載您的 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [檢視及下載 Microsoft 客戶合約的稅務文件](billing-mca-download-tax-document.md)
- [檢視及下載組織的 Azure 定價](billing-ea-pricing.md)
