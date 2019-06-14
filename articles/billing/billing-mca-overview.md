---
title: 開始使用您的帳單帳戶的 Microsoft 客戶合約-Azure |Microsoft Docs
description: 了解 Microsoft 的客戶合約帳單帳戶
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371442"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 客戶合約開始使用您的帳單帳戶

針對每份合約，若要使用 Azure 登入與 Microsoft 建立計費的帳戶。 您可以使用您的帳單帳戶來管理計費和追蹤成本。 您可以有多個計費帳戶的存取權。 比方說，您可能已註冊 Azure 為您個人的專案。 您也可以準備透過組織的 Enterprise 合約或 Microsoft 客戶合約的 Azure 存取權。 針對每個這些案例中，您必須使用個別的帳單帳戶。

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

## <a name="understand-billing-account"></a>了解帳單帳戶

您的 Microsoft 客戶合約的帳單帳戶包含一或多個計費的設定檔，讓您管理您的發票和付款方法。 每個計費的設定檔包含一或多個發票區段可讓您組織的帳單設定檔的發票上的成本。

下圖顯示帳單帳戶、帳單設定檔和發票區段之間的關聯性。

![此圖顯示 Microsoft 客戶合約計費階層](./media/billing-mca-overview/mca-billing-hierarchy.png)

計費帳戶的角色都有最高層級的權限。 根據預設，只有在您組織的 Azure Active Directory 的全域系統管理員會取得計費帳戶的存取權。 這些角色應指派給需要檢視發票，並追蹤成本，例如財務人員或 IT 管理整個組織的使用者。 如需詳細資訊，請參閱 <<c0> [ 計費帳戶的角色和工作](billing-understand-mca-roles.md#billing-account-roles-and-tasks)。

## <a name="understand-billing-profiles"></a>了解計費的設定檔

您可以使用 計費的設定檔來管理您的發票與付款方法。 Azure 訂用帳戶和計費的設定檔的使用購買其他產品，會產生每月的發票。 您可以使用的付款方法要按發票付款。

帳單的設定檔會自動建立為帳單帳戶。 您可以建立新的計費設定檔，以設定其他的發票。 比方說，您可以不同的發票針對每個部門或專案中您的組織。

您也可以建立發票區段，以組織的計費設定檔的發票上的成本。 Azure 訂用帳戶和發票區段購買產品的費用會顯示上一節。 計費的設定檔的發票包括費用可用於所有發票區段。

計費的設定檔上的角色有權限來檢視和管理發票與付款方法。 支付發票，例如會計部門的成員，您組織中的使用者指派這些角色。 如需詳細資訊，請參閱 <<c0> [ 帳單設定檔角色和工作](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>針對每個計費的設定檔產生的每月發票

發票日期的每個計費的設定檔，會產生每月的發票。 發票會包含上個月的所有費用。

您可以檢視發票、 下載文件，並將設定變更為 Azure 入口網站中的電子郵件，以取得未來的發票。 如需詳細資訊，請參閱 <<c0> [ 下載發票的 Microsoft 客戶合約](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

### <a name="invoices-paid-through-payment-methods"></a>透過付款方式支付發票

每個計費的設定檔有它自己用來支付其發票的付款方法。 支援下列的付款方法：

| 類型             | 定義  |
|------------------|-------------|
|Azure 信用額度    |  信用額度會自動套用計費來計算量，您需支付您發票上的總量。 如需詳細資訊，請參閱 <<c0> [ 追蹤帳單設定檔的 Azure 信用額度餘額](billing-mca-check-azure-credits-balance.md)。 |
|核取或網路傳輸 | 您可以使用付款金額發票透過核取或網路傳輸。 發票會提供付款指示 |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>藉由套用原則控制 Azure Marketplace 並保留購買項目

套用原則以控制購買的產品使用計費的設定檔。 您可以設定原則來停用購買 Azure 保留項目和 Marketplace 產品。 會套用原則，為帳單的設定檔的發票章節建立的訂用帳戶不能購買 Azure 保留項目和 Marketplace 產品中。

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>允許使用者藉由啟用 Azure 方案中建立 Azure 訂用帳戶

當您建立的計費的設定檔時，會自動啟用 azure 方案。 計費的設定檔中的所有發票區段都取得這些計劃。 使用者的存取權 [發票] 區段會使用計劃，來建立 Azure 訂用帳戶。 他們無法建立 Azure 訂用帳戶，除非 Azure 方案已啟用可計費的設定檔。 下列的 Azure 方案支援的 Microsoft 客戶合約計費帳戶：

| 規劃             | 定義  |
|------------------|-------------|
|Microsoft Azure Plan   | 允許使用者建立訂用帳戶，可以執行任何工作負載。 如需詳細資訊，請參閱[計劃的 Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Microsoft Azure 開發/測試的計劃 | 允許 Visual Studio 訂閱者建立訂用帳戶不允許的開發或測試工作負載。 這些訂用帳戶在 Azure 入口網站中取得的優點，例如較低的費率和專屬的虛擬機器映像的存取。 如需詳細資訊，請參閱[研發/測試的計劃的 Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>了解發票區段

建立組織帳單設定檔的發票費用的發票區段。 例如，您可能需要單一的發票，為您的組織，但您想来組織部門、 小組或專案的成本。 此案例中，您有單一帳單設定檔，您會建立每個部門、 小組或專案的發票區段。

建立發票區段時，您可以讓其他人建立區段的 Azure 訂用帳戶的權限。 然後，任何使用費用和訂用帳戶的購買項目會反映在發票的適當章節。

[發票] 區段的角色有權限來控制會建立 Azure 訂用帳戶。 設定 Azure 環境，讓小組在我們的組織，例如工程的潛在客戶和技術架構設計人員的使用者指派這些角色。 如需詳細資訊，請參閱 <<c0> [ 發票區段角色和工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

請參閱下列文章以深入了解您的帳單帳戶：

- [了解在 Azure 中的 Microsoft 客戶合約系統管理角色](billing-understand-mca-roles.md)
- [Microsoft 客戶協議建立額外的 Azure 訂用帳戶](billing-mca-create-subscription.md)
- [若要組織您的成本您發票上建立區段](billing-mca-section-invoice.md)