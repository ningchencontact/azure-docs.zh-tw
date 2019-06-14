---
title: 檢視和下載 Azure 使用量和費用 |Microsoft Docs
description: 說明如何下載或檢視您的 Azure 每日使用量和費用。
keywords: 計費使用量、 使用費用，使用方式下載，請檢視使用量，azure 發票，azure 使用量
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918972"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>檢視及下載您的 Azure 使用量和費用

如果您是 EA 客戶，或有[Microsoft 客戶合約](#check-your-access-to-a-microsoft-customer-agreement)，您可以下載 Azure 使用量和費用中的[Azure 入口網站](https://portal.azure.com/)。 其他訂用帳戶，請移至[Azure 帳戶中心](https://account.azure.com/Subscriptions)下載使用量。

只有某些角色有權限，以取得 Azure 使用量的資訊，例如帳戶管理員或企業系統管理員。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](billing-manage-access.md)。

如果您有[Microsoft 客戶合約](#check-your-access-to-a-microsoft-customer-agreement)，您必須是帳單設定檔擁有者、 參與者、 讀取器或管理員來檢視您的 Azure 使用量和費用的發票。 若要深入了解計費角色適用於 Microsoft 客戶合約，請參閱[帳單設定檔角色和工作](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

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

若要檢視及下載 EA 客戶的使用量資料，您必須是企業系統管理員，帳戶擁有者，或部門系統管理員與檢視費用啟用原則。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋*成本管理 + 計費*。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. 選取 [使用量 + 費用]  。
1. 針對您想要下載的月份，選取 [下載]  。

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Microsoft 客戶合約下載使用量

如果您有 Microsoft 客戶合約時，您可以下載您的 Azure 使用量和費用帳單設定檔。 您必須是帳單設定檔擁有者、 參與者、 讀取器，或發票 manager 下載的 Azure 使用量和費用 CSV。

### <a name="download-usage-for-billed-charges"></a>下載使用量計費的費用

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 針對 [成本管理 + 帳單]  進行搜尋。
3. 選取計費的設定檔。 根據您的存取權，您可能需要先選取計費帳戶。
4. 選取 [發票]  。
5. 在發票方格中，尋找對應至您想要下載使用量發票的資料列。
6. 按一下省略符號 (`...`) 結尾的資料列。

    ![顯示的資料列結尾的省略符號的螢幕擷取畫面](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. 在下載內容功能表中，選取**Azure 使用量和費用**。

     ![顯示 Azure 使用量和費用選取螢幕擷取畫面](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>下載使用量的暫止的費用

您也可以下載月-日使用情況，目前的計費週期。 這些尚未有不已計費的使用量費用。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 針對 [成本管理 + 帳單]  進行搜尋。
3. 選取計費的設定檔。 根據您的存取權，您可能需要先選取計費帳戶。
4. 在 **概觀**刀鋒視窗中，尋找下方至今的月份費用的下載連結。
5. 選取  **Azure 使用量和費用**。

    ![從 [概觀] 顯示下載的螢幕擷取畫面](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>請檢查您的存取權的 Microsoft 客戶合約
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您發票和使用量的費用，請參閱：

- [了解您 Microsoft Azure 詳細使用量上的字詞](billing-understand-your-usage.md)
- [了解 Microsoft Azure 帳單](billing-understand-your-bill.md)
- [檢視及下載您 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [檢視及下載您的組織 Azure 定價](billing-ea-pricing.md)

如果您有 Microsoft 的客戶合約，請參閱：

- [了解條款，在您的 Microsoft 客戶合約 Azure 詳細使用量](billing-mca-understand-your-usage.md)
- [了解 Microsoft 的客戶合約發票費用](billing-mca-understand-your-bill.md)
- [檢視及下載您 Microsoft Azure 發票](billing-download-azure-invoice.md)
- [檢視及下載 Microsoft 客戶合約的稅務文件](billing-mca-download-tax-document.md)
- [檢視及下載您的組織 Azure 定價](billing-ea-pricing.md)
