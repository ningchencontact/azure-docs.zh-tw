---
title: 按發票支付 Azure 訂閱費
description: 描述如何按發票支付 Azure 訂用帳戶。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: 9e4e05acd88e9b0f0c17d4dd4caf5eb5a883d63d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70012603"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>按發票支付 Azure 訂閱費

如果您切換為按發票付款，這表示您會在發票日期的 30 天內使用支票/電匯支付帳單費用。 若要符合按發票支付 Azure 訂用帳戶費用的資格，請將要求提交給 Azure 支援。 一旦您的要求獲得核准，您就可以在 [Azure 入口網站](https://portal.azure.com)中切換為發票付款 (支票/電匯)。

> [!IMPORTANT]
> * 發票付款 (支票/電匯) 只適用於商務帳戶。
> * 在切換為發票付款之前，請支付所有未付費用。
> * 目前，中國的全球 Azure 不支援發票付款。

## <a name="request-to-pay-by-invoice"></a>按發票付款的要求

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 選取 [說明+支援]   > [新增支援要求]  。

    ![說明與支援連結](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. 選取 [帳單]  作為 [問題類型]  。 「問題類型」  為支援要求類別。 選取您想要依發票付款的訂用帳戶、選取支援方案，然後選取 [下一步]  。

3. 選取 [付款]  作為 [問題類型]  。 「問題類型」  為支援要求子類別。

4. 選取 [切換為按發票付款]  作為 [問題子類型] 

5. 在 [詳細資料]  方塊中輸入下列資訊，並選取 [下一步]  。

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - [公司名稱]  和 [公司位址]  應符合您為 Azure 帳戶提供的資訊。 若要檢視或更新資訊，請參閱[變更您的 Azure 帳戶設定檔資訊](billing-how-to-change-azure-account-profile.md)。
    - 請先在 Azure 入口網站中新增您的帳單連絡人資訊，才能核准信用限額。 連絡人詳細資料應該與公司的應付帳款或財務部門相關。 若要更新帳單連絡人資訊，請移至 [Azure 帳戶中心](https://account.azure.com/Profile)。

6. 確認您的連絡資訊和慣用的連絡方法，然後按一下 [建立]  。

如果我們因您所需要的信用額度而必須進行信用查核，我們會將信用查核應用程式傳送給您。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>切換為發票付款 (支票/電匯)

一旦您獲准按發票付款後，您就可以在 Azure 入口網站中切換為發票付款 (支票/電匯)。

如果您有 Microsoft Online Services 方案帳戶，您可以將 Azure 訂用帳戶切換為支票/電匯。 擁有 Microsoft 客戶合約，您可以將帳單設定檔切換為支票/電匯。 [了解如何檢查您的帳戶類型](#check-access-to-a-microsoft-customer-agreement)。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>將 Azure 訂用帳戶切換為支票/電匯

請遵循下列步驟，將您的 Azure 訂用帳戶切換為發票付款 (支票/電匯)。 **一旦您切換為發票付款 (支票/電匯) 後，就無法切換回信用卡付款。**

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示搜尋的螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/search.png)

1. 選取您要切換至發票付款的訂用帳戶。
1. 選取 [**付款方式**]。
1. 在命令列中，按一下 [按發票付款]  按鈕。

    ![顯示按發票付款按鈕的螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>將帳單設定檔切換為支票/電匯

請遵循下列步驟，將帳單設定檔切換為支票/電匯。 只有註冊 Azure 的人員可以變更帳單設定檔的預設付款方式。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。
1. 在左側功能表中，按一下 [帳單設定檔]  。

    ![在功能表中顯示帳單設定檔的螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. 選取帳單設定檔。
1. 在左側功能表中，選取 [付款方式]  。

   ![在功能表中顯示付款方式的螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. 按一下藍色橫幅，指出您有資格透過支票/電匯付款。

    ![顯示藍色橫幅以切換為支票/電匯的螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 如有需要，請在 [Azure 帳戶中心](https://account.azure.com/Profile)更新您的帳單連絡人資訊。
