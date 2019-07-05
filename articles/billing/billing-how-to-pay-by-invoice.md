---
title: 依發票支付 Azure 訂用帳戶
description: 描述如何依發票支付 Azure 訂用帳戶。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491258"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>依發票支付 Azure 訂用帳戶

如果您切換到發票付款，這表示您的核取/網路傳輸的發票日期的 30 天內支付您的帳單。 若要成為合格發票支付 Azure 訂用帳戶，提交要求給 Azure 支援。 一旦您的要求核准之後，您可以在切換到發票付款 （核取/寫入傳輸） [Azure 入口網站](https://portal.azure.com)。

> [!IMPORTANT]
> * 發票支付 （核取/寫入傳輸） 只適用於商務帳戶。
> * 切換到發票支付之前，您必須支付所有未付費用。

## <a name="request-to-pay-by-invoice"></a>若要依發票付款的要求

1. 登入 [Azure 入口網站](https://portal.azure.com/)。 選取 [說明+支援]   > [新增支援要求]  。

    ![說明及支援 連結](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. 選取 **計費**作為**問題類型**。 *問題類型*支援要求類別目錄。 選取您要依發票付款，選取 [支援方案，並選取訂用的帳戶**下一步]** 。

3. 在 [問題類型]  方塊中選取 [按發票付款]  選項。 *問題類型*是支援要求的子類別目錄。

4. 在 [詳細資料]  方塊中輸入下列資訊，並選取 [下一步]  。

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

    - **公司名稱**並**公司地址**應符合您的 Azure 帳戶所提供的資訊。 若要檢視或更新的資訊，請參閱[變更您的 Azure 帳戶設定檔資訊](billing-how-to-change-azure-account-profile.md)。
    - 可以核准的信用額度，您必須在 Azure 入口網站中新增您的帳單連絡人資訊。 應該與公司的 Accounts Payable 或財務部門相關之連絡人詳細資料。 若要更新的計費的連絡資訊，請前往[Azure 帳戶中心](https://account.azure.com/Profile)。

5. 確認您的連絡資訊和慣用的連絡方法，然後按一下 [建立]  。

如果我們需要進行信用查核因為您需要的信用額度的金額，我們會傳送您的信用額度檢查應用程式。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>切換到發票支付 （核取/寫入傳輸）

一旦您獲准依發票付款，您可以切換到發票支付 （核取/寫入傳輸），在 Azure 入口網站中。

如果您有 Microsoft Online Services 的計劃帳戶，您可以切換核取/網路傳輸至您 Azure 訂用帳戶。 如果您有 Microsoft 客戶合約時，您可以切換核取/網路傳輸至帳單設定檔。 [了解如何檢查您的帳戶類型](#check-access-to-a-microsoft-customer-agreement)。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>切換核取/網路傳輸至 Azure 訂用帳戶

請遵循下列步驟來切換 Azure 訂用帳戶發票支付 （核取/寫入傳輸）。 **一旦您切換到發票支付 （核取/寫入傳輸） 您無法切換回信用卡**。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋**成本管理 + 計費**。

    ![顯示搜尋螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/search.png)

1. 選取您想要切換到發票付款的訂用帳戶。
1. 選取 [付款方式]  。
1. 在 命令 列中，按一下**依發票付款** 按鈕。

    ![螢幕擷取畫面顯示支付發票 按鈕](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>計費以核取/網路傳輸的設定檔的參數

請遵循下列步驟來切換核取/網路傳輸至帳單設定檔。 請注意，只有已註冊 Azure 的人員可以變更帳單的設定檔的預設付款方法。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。
1. 在左側功能表中，按一下**帳單設定檔**。

    ![顯示計費的設定檔功能表中的螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. 選取計費的設定檔。
1. 在左側功能表中，選取**付款方法**。

   ![在功能表中顯示的付款方法的螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. 按一下藍色橫幅： 即符合資格的核取/電匯付費。

    ![若要切換到核取/寫入的藍色橫幅會顯示的螢幕擷取畫面](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 如有需要更新在帳單連絡人資訊[Azure 帳戶中心](https://account.azure.com/Profile)。
