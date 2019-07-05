---
title: 開始使用 Microsoft 客戶合約帳單帳戶-Azure
description: 了解您的 Microsoft 的客戶合約帳單帳戶
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490741"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>開始使用您的 Microsoft 客戶合約帳單帳戶

當您使用 Azure 註冊時，會建立帳單帳戶。 您會使用您的帳單帳戶管理發票付款，並且追蹤成本。 您可以有多個計費帳戶的存取權。 比方說，您可能已註冊 Azure 為您個人的專案。 您也可以準備透過組織的 Enterprise 合約或 Microsoft 客戶合約的 Azure 存取權。 針對每個這些案例中，您必須使用個別的帳單帳戶。

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

## <a name="your-billing-account"></a>您的帳單帳戶

您的 Microsoft 客戶合約的帳單帳戶包含一或多個計費的設定檔，讓您管理您的發票和付款方法。 每個計費的設定檔包含一或多個發票區段可讓您組織的帳單設定檔的發票上的成本。

下圖顯示的帳單帳戶、 計費的設定檔和發票區段之間的關聯性。

![顯示 Microsoft 的客戶合約計費階層架構圖表](./media/billing-mca-overview/mca-billing-hierarchy.png)

計費帳戶的角色都有最高層級的權限。 根據預設，只有已註冊 Azure 取得的存取權的計費帳戶的使用者。 這些角色應指派給需要檢視發票，並追蹤成本，例如財務人員或 IT 管理整個組織的使用者。 如需詳細資訊，請參閱 <<c0> [ 計費帳戶的角色和工作](billing-understand-mca-roles.md#billing-account-roles-and-tasks)。

## <a name="billing-profiles"></a>帳單設定檔

您可以使用 計費的設定檔來管理您的發票與付款方法。 在每個計費的設定檔，在您的帳戶中的月份開始，會產生每月的發票。 發票會包含所有的 Azure 訂用帳戶和其他購買項目，從上一個月份的個別費用。

帳單的設定檔會自動建立為帳單帳戶。 它預設包含一個 「 發票 」 一節。 您也可以建立其他的區段，以輕鬆追蹤及組織您的需求為基礎的成本是否每個專案、 部門或開發環境。 您會看到這些區段反映每個訂用帳戶和購買項目指派給它的使用量計費的設定檔的發票上。

計費的設定檔上的角色有權限來檢視和管理發票與付款方法。 支付發票，例如會計部門的成員，您組織中的使用者指派這些角色。 如需詳細資訊，請參閱 <<c0> [ 帳單設定檔角色和工作](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>每個計費的設定檔取得每月的發票

在每個計費的設定檔的月份開始，會產生每月的發票。 發票會包含上個月的所有費用。

您可以檢視發票、 下載文件，並將設定變更為 Azure 入口網站中的電子郵件，以取得未來的發票。 如需詳細資訊，請參閱 <<c0> [ 下載發票的 Microsoft 客戶合約](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="invoice-payment-methods"></a>發票付款方法

每個計費的設定檔有它自己用來支付其發票的付款方法。 支援下列的付款方法：

| 類型             | 定義  |
|------------------|-------------|
|Azure 信用額度    |  信用額度會自動套用您在發票上，減少您需要支付符合資格的費用。 如需詳細資訊，請參閱 <<c0> [ 追蹤帳單設定檔的 Azure 信用額度餘額](billing-mca-check-azure-credits-balance.md)。 |
|核取/網路傳輸 | 如果您的帳戶已透過核取/轉帳付款核准。 您可以用多少付多少數量到期發票透過核取/網路傳輸。 發票會提供付款指示 |
|信用卡 | 註冊 Azure 透過 Azure 網站的客戶可以透過一張信用卡付款。 |

### <a name="apply-policies-to-control-purchases"></a>套用原則以控制購買項目

將原則套用至控制 Azure Marketplace 與使用計費的設定檔的保留項目購買。 您可以設定原則來停用購買 Azure 保留項目和 Marketplace 產品。 將原則套用，帳單的設定檔收費的訂用帳戶不能讓這些購買項目中。

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure 方案決定為訂用帳戶的定價和服務等級協定

Azure 方案決定的價格，以及服務等級協定的 Azure 訂用帳戶。 當您建立計費的設定檔會自動啟用。 帳單的設定檔相關聯的所有發票區段可以都使用這些計劃。 使用者的存取權 [發票] 區段會使用計劃，來建立 Azure 訂用帳戶。 下列的 Azure 方案支援的 Microsoft 客戶合約計費帳戶：

| 規劃             | 定義  |
|------------------|-------------|
|Microsoft Azure Plan   | 允許使用者建立訂用帳戶，可以執行任何工作負載。 如需詳細資訊，請參閱[計劃的 Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Microsoft Azure 開發/測試的計劃 | 允許 Visual Studio 訂閱者建立訂用帳戶不允許的開發或測試工作負載。 這些訂用帳戶在 Azure 入口網站中取得的優點，例如較低的費率和專屬的虛擬機器映像的存取。 如需詳細資訊，請參閱[研發/測試的計劃的 Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>發票區段

建立發票區段，來組織您發票上的成本。 例如，您可能需要單一的發票，為您的組織，但您想来組織部門、 小組或專案的成本。 此案例中，您有單一帳單設定檔，您會建立每個部門、 小組或專案的發票區段。

建立發票區段時，您可以讓其他人建立計費一節的 Azure 訂用帳戶的權限。 任何使用費用和訂用帳戶購買會計費一節。

[發票] 區段的角色有權限來控制會建立 Azure 訂用帳戶。 設定 Azure 環境，讓小組在我們的組織，例如工程的潛在客戶和技術架構設計人員的使用者指派這些角色。 如需詳細資訊，請參閱 <<c0> [ 發票區段角色和工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

請參閱下列文章以深入了解您的帳單帳戶：

- [了解在 Azure 中的 Microsoft 客戶合約系統管理角色](billing-understand-mca-roles.md)
- [Microsoft 客戶協議建立額外的 Azure 訂用帳戶](billing-mca-create-subscription.md)
- [若要組織您的成本您發票上建立區段](billing-mca-section-invoice.md)
