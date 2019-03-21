---
title: 完成 Microsoft 客戶合約-Azure 中的 Enterprise 合約工作 |Microsoft Docs
description: 了解如何完成中新的計費帳戶的 Enterprise 合約工作。
services: ''
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
ms.date: 02/24/2018
ms.author: banders
ms.openlocfilehash: 9b76fa935263904957c87cd062c84d0607771369
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341370"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 客戶合約完成您的帳單帳戶中的 Enterprise 合約工作

如果您的組織已簽署更新您的 Enterprise 合約註冊的 Microsoft 客戶合約，新的計費帳戶會建立協議。 在您的新帳戶的計費被組織的方式不同於您的 Enterprise 合約。 本文說明如何使用新的計費帳戶來執行您在您的 Enterprise 合約中執行的工作。

## <a name="how-billing-is-organized-in-the-new-account"></a>計費在新的帳戶中的組織方式

下圖說明如何計費依照您新的計費帳戶。

![Ea-mca-post-轉換-階層的映像](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

| Enterprise 合約   | Microsoft 客戶合約    |
|------------------------|--------------------------------------------------------|
| 申請            | 您可以使用計費的設定檔來為您的組織，類似於您的 Enterprise 合約註冊管理計費。 企業系統管理員會成為擁有者的帳單的設定檔。 若要深入了解計費的設定檔，請參閱[了解計費的設定檔](billing-mca-overview.md#understand-billing-profiles)。
| department            | 您可以使用 [發票] 區段來組織您的成本，類似於您的 Enterprise 合約註冊中的部門。 部門變成發票區段和部門系統管理員發揮各自的發票區段的擁有者。 若要深入了解發票區段，請參閱[了解發票區段](billing-mca-overview.md#understand-invoice-sections)。 |
| 帳戶               | 您的 Enterprise 合約中所建立的帳戶並不支援新的計費帳戶。 帳戶的訂用帳戶隸屬於其系所的個別發票區段。 帳戶擁有者可以建立和管理訂用帳戶，其 「 發票 」 章節。 |

## <a name="changes-for-enterprise-administrators-in-the-new-billing-account"></a>在新的計費帳戶中的企業系統管理員的變更

下列變更會套用到已更新至 Microsoft 客戶協議的 Enterprise 合約的企業系統管理員。

- 帳單的設定檔會建立您的註冊。 若要為您的組織管理計費，例如 Enterprise 合約註冊，您將使用帳單的設定檔。 若要深入了解計費的設定檔[了解計費的設定檔](billing-mca-overview.md#understand-billing-profiles)。
- 在您的 Enterprise 合約註冊的每個部門建立發票一節。 您將使用發票各節來管理您的部門。 您可以建立新的發票幾節，來設定其他部門。 若要深入了解發票區段，請參閱[了解發票區段](billing-mca-overview.md#understand-invoice-sections)。
- 您將使用發票各節中的 Azure 訂用帳戶建立者角色授與其他人建立 Azure 訂用帳戶，例如 Enterprise 合約註冊中所建立的帳戶的權限。
- 您將使用[Azure 入口網站](https://portal.azure.com)來管理您的組織，而不是 Azure EA 入口網站的計費。

新的計費帳戶，您會獲得下列角色：

**帳單設定檔擁有者**-您已獲得計費的設定檔擁有者角色，在計費時已簽署的協議所建立的設定檔。 角色可讓您管理貴組織的計費。 您可以檢視費用和發票、 組織成本，發票上的付款方法和控制存取您的組織計費。

**發票區段擁有者**-皆會針對您的 Enterprise 合約註冊中的部門的所有發票區段的發票區段擁有者角色。 角色可讓您控制可以建立 Azure 訂用帳戶和購買其他產品。

### <a name="view-charges-and-credits-balance-for-your-organization"></a>檢視費用和信用額度餘額，為您的組織

您可以使用帳單設定檔來追蹤組織類似於您的 Enterprise 合約註冊的費用和 Azure 信用額度餘額。

若要了解如何檢視計費的設定檔的信用結餘，請參閱[追蹤帳單設定檔的 Azure 信用額度餘額](billing-mca-check-azure-credits-balance.md)。

若要了解如何檢視計費的設定檔的費用，請參閱[了解您的 Microsoft 客戶協議的發票費用](billing-mca-understand-your-bill.md)。

### <a name="view-charges-for-a-department"></a>檢視某部門的費用

您必須在您的 Enterprise 合約中每個部門建立發票一節。 您可以在 Azure 入口網站中檢視費用的發票區段。 如需詳細資訊，請參閱 <<c0> [ 檢視發票區段的最前面交易](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)。

### <a name="view-charges-for-an-account"></a>檢視帳戶的費用

在新的計費帳戶中不支援您的 Enterprise 合約註冊中所建立的帳戶。 帳戶的訂用帳戶隸屬於其系所的個別發票區段。 帳戶擁有者可以建立和管理訂用帳戶，其 「 發票 」 章節。

若要檢視屬於帳戶的訂用帳戶的彙總成本，您必須設定每個訂用帳戶的成本中心。 然後，您可以篩選根據成本中心的訂用帳戶使用 Azure 的使用量和費用 csv 檔案。

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>下載使用量和費用 csv、 價位表和稅務文件

每個計費的設定檔，在您的帳單帳戶中，會產生每月的發票。 針對每筆發票，您可以下載 Azure 的使用量和費用 csv 檔案、 價位表和稅務文件 （如果適用）。 您也可以下載目前月份的費用的 Azure 使用量和費用 csv 檔案。

若要了解如何下載 Azure 的使用量和費用 csv 檔案，請參閱[Microsoft 客戶合約下載使用量](billing-download-azure-daily-usage.md#download-usage-for-your-microsoft-customer-agreement)。

若要了解如何下載價位表，請參閱[下載您的 Microsoft 客戶合約的定價](billing-ea-pricing.md#view-and-download-pricing-for-your-microsoft-customer-agreement)。

若要了解如何下載稅務文件，請參閱[稅務文件檢視您的 Microsoft 客戶合約](billing-mca-download-tax-document.md#view-and-download-tax-documents)。

### <a name="add-an-additional-enterprise-administrator"></a>新增其他企業系統管理員

讓使用者存取帳單的設定檔，讓他們檢視並管理貴組織的計費。 您可以使用**存取控制 (IAM)** 提供存取 Azure 入口網站中的頁面。  若要深入了解帳單設定檔角色，請參閱[帳單設定檔角色和工作](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

若要了解如何提供、 帳單設定檔的存取，請參閱[管理 Azure 入口網站中的計費角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="create-a-new-department"></a>建立一個新的部門

建立發票區段來組織您根據您的需求，例如在您的 Enterprise 合約註冊的部門的成本。 您可以在 Azure 入口網站中建立新的 「 發票 」 一節。 若要進一步了解，請參閱[來組織您的成本發票建立區段](billing-mca-section-invoice.md)。

### <a name="create-a-new-account"></a>建立新帳戶

將使用者指派發票各節提供這些權限來建立 Azure 訂用帳戶，例如 Enterprise 合約註冊中建立帳戶的 Azure 訂用帳戶建立者角色。 如需詳細資訊，請參閱 <<c0> [ 讓其他人建立 Azure 訂用帳戶的權限](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions)。

## <a name="changes-for-department-administrators-in-the-new-billing-account"></a>在新的計費帳戶中的部門系統管理員的變更

下列變更會套用到 Microsoft 的客戶合約已更新了 Enterprise 合約的部門系統管理員。

- 在您的 Enterprise 合約註冊的每個部門建立發票一節。 您將使用發票區段來管理您 department(s)。 若要深入了解發票區段，請參閱[了解發票區段](billing-mca-overview.md#understand-invoice-sections)。
- 您將使用 [發票] 區段中的 Azure 訂用帳戶建立者角色授與其他人建立 Azure 訂用帳戶，例如 Enterprise 合約註冊中建立帳戶的權限。
- 您將使用 Azure 入口網站來為您的組織，而不是 Azure EA 入口網站中管理計費。

新的計費帳戶，您會獲得下列角色：

**發票區段擁有者**-您已獲得發票區段的擁有者角色，系統會為您的 Enterprise 合約中有 department(s) 的 [發票] 區段。 角色可讓您檢視和追蹤費用和控制可以建立 Azure 訂用帳戶和購買其他產品的 [發票] 區段。

### <a name="view-charges-for-your-department"></a>您部門的的檢視費用

您可以檢視您的部門，請在 Azure 入口網站中建立的 [發票] 區段的費用[成本管理 + 帳單 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。

### <a name="add-an-additional-department-administrator"></a>新增其他部門的系統管理員

您必須在您的 Enterprise 合約中每個部門建立發票一節。 您可以使用**存取控制 （iam)** 在 Azure 入口網站，讓其他人存取來檢視和管理 [發票] 區段中的頁面。 若要深入了解發票區段角色，請參閱[發票區段角色和工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

若要了解如何提供、 發票區段的存取，請參閱[管理 Azure 入口網站中的計費角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="create-a-new-account-in-your-department"></a>在您的部門建立新的帳戶

將使用者指派的 Azure 訂用帳戶建立者角色，系統會為您部門的 [發票] 區段。 如需詳細資訊，請參閱 <<c0> [ 讓其他人建立 Azure 訂用帳戶的權限](billing-mca-create-subscription.md#give-others-permission-to-create-azure-subscriptions)。

### <a name="view-charges-for-accounts-in-your-departments"></a>檢視費用，為您部門中的帳戶

在新的計費帳戶中不支援您的 Enterprise 合約註冊中所建立的帳戶。 帳戶的訂用帳戶隸屬於其系所的個別發票區段。 帳戶擁有者可以建立和管理訂用帳戶，其 「 發票 」 章節。

若要檢視您的部門中的帳戶屬於訂用帳戶的彙總成本，您必須設定每個訂用帳戶的成本中心。 然後，您可以篩選根據成本中心的訂用帳戶使用 Azure 的使用量和費用檔案。

## <a name="changes-for-account-owners-in-the-new-billing-account"></a>中新的計費帳戶的帳戶擁有者的變更

在 Enterprise 合約上的帳戶擁有者取得新的計費帳戶建立 Azure 訂用帳戶的權限。 您現有的 Azure 訂用帳戶隸屬於系統會為您部門的 [發票] 區段。 如果您的帳戶不屬於一個部門，您的訂用帳戶會屬於名為預設 [發票] 區段的發票區段。

若要建立其他的 Azure 訂用帳戶，您會提供新的計費帳戶的下列角色。

**Azure 訂用帳戶的建立者**-您為您的部門在企業合約中指派發票一節所建立的 azure 訂用帳戶建立者角色。 如果您的帳戶不屬於一個部門，您會取得名為預設 [發票] 區段的區段上的 Azure 訂用帳戶建立者角色。 角色可讓您建立的 [發票] 區段的 Azure 訂用帳戶。

### <a name="create-an-azure-subscription"></a>建立 Azure 訂用帳戶

您可以為您在 Azure 入口網站中的 [發票] 區段，來建立 Azure 訂用帳戶。 如需詳細資訊，請參閱[建立額外的 Azure 訂用帳戶的 Microsoft 客戶合約](billing-mca-create-subscription.md)

### <a name="view-charges-for-your-account"></a>檢視您帳戶的費用

若要檢視屬於帳戶的訂用帳戶計費，請前往[訂用帳戶頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)在 Azure 入口網站中。 [訂閱] 頁面會顯示所有訂用帳戶的費用。

### <a name="view-charges-for-a-subscription"></a>檢視訂用帳戶的費用

您可以檢視訂用帳戶的費用，請在[訂用帳戶頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)或成本分析的 Azure。 如需有關 Azure 的成本分析的詳細資訊，請參閱 <<c0> [ 探索及分析成本，以及成本分析](../cost-management/quick-acm-cost-analysis.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [了解 Microsoft 的客戶合約帳單帳戶](billing-mca-overview.md)
- [了解發票](billing-understand-your-bill.md)
- [了解您的帳單](billing-understand-your-invoice.md)
- [取得帳單其他使用者的 Azure 訂用帳戶的擁有的權](billing-mca-request-billing-ownership.md)
