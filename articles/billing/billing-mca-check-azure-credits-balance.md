---
title: 追蹤 Microsoft 客戶合約的 Azure 點數餘額
description: 了解如何查看 Microsoft 客戶合約的 Azure 點數餘額。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375556"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>追蹤 Microsoft 客戶合約的 Azure 點數餘額

您可以在 Azure 入口網站中針對 Microsoft 客戶合約查看您計費帳戶的 Azure 點數餘額。 

您可以使用點數來支付有資格使用點數的費用。 若您使用的產品沒有資格使用點數，或您的使用量超過點數餘額，您就必須支付費用。 如需詳細資訊，請參閱 [Azure 點數未涵蓋的產品](#products-that-arent-covered-by-azure-credits)。

在 Microsoft 客戶合約的計費帳戶中，點數會指派給帳單設定檔。 每個帳單設定檔都有自己的點數。 您必須擁有帳單設定檔的擁有者、參與者、讀者或發票管理員角色，或計費帳戶的擁有者、參與者或讀者角色，才能檢視帳單設定檔的 Azure 點數餘額。 若要深入了解角色，請參閱[了解 Azure 中的 Microsoft 客戶合約管理角色](billing-understand-mca-roles.md)。

本文適用於 Microsoft 客戶合約的計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-access-to-a-microsoft-customer-agreement)。

## <a name="check-your-credit-balance-in-the-azure-portal"></a>在 Azure 入口網站中檢查您的點數餘額

1. 登入 [Azure 入口網站]( https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

    ![顯示在入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  從左側選取 [Azure 點數]  。 視存取權之不同，您可能必須選取計費帳戶或帳單設定檔，然後選取 [Azure 點數]  。

4. [Azure 點數] 頁面會顯示下列資訊：

   ![帳單設定檔的點數餘額和交易的螢幕擷取畫面](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 詞彙               | 定義                           |
   |--------------------|--------------------------------------------------------|
   | 估計餘額  | 您在考量所有計費交易和擱置交易後所擁有的估計點數金額 |
   | 目前餘額    | 截至最後一張發票為止的點數金額。 其中不包含任何擱置交易 |
   | 交易       | 對 Azure 點數餘額造成影響的帳單交易 |

   當您的估計餘額降到 0 時，您所有的使用量都必須付費，包括有資格使用點數的產品。

6. 選取 [點數清單]  ，以檢視帳單設定檔的點數清單。 點數清單會提供下列資訊：

   ![帳單設定檔點數清單的螢幕擷取畫面](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 詞彙 | 定義 |
   |---|---|
   | 來源 | 點數的取得來源 |
   | 開始日期 | 您取得點數的日期 |
   | 到期日期 | 點數到期的日期 |
   | 目前餘額 | 截至最後一張發票為止的餘額 |
   | 原始金額 | 原始點數金額 |
   | 狀態 | 點數的目前狀態。 狀態可以是有效、已使用、已過期或即將到期 |

## <a name="check-your-credit-balance-programmatically"></a>以程式設計方式檢查您的點數餘額

您可以使用 [Azure 計費](https://docs.microsoft.com/rest/api/billing/)和[使用量](https://docs.microsoft.com/rest/api/consumption/) API，以程式設計方式取得計費帳戶的點數餘額。

以下顯示的範例使用 REST API。 目前不支援 PowerShell 和 Azure CLI。

### <a name="find-billing-profiles-you-have-access-to"></a>尋找您有權存取的帳單設定檔

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
API 回應會傳回計費帳戶及其帳單設定檔的清單。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

使用帳單設定檔的 `displayName` 屬性，識別您要檢查點數餘額的帳單設定檔。 複製帳單設定檔的 `id`。 例如，如果您想要檢查 **Development** 帳單設定檔的點數餘額，您會複製 ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```。 將此值貼在某處，以便您在下一個步驟中使用。

### <a name="get-azure-credit-balance"></a>取得 Azure 點數餘額 

提出下列要求，並以從第一個步驟複製的 `id` 取代 `<billingProfileId>` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)。 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

API 回應會傳回帳單設定檔的預估和目前餘額。

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| 元素名稱  | 說明                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | 您在考量所有計費交易和擱置交易後所擁有的估計點數金額。 |
| `currentBalance`   | 截至最後一張發票為止的點數金額。 其中不包含任何擱置交易。    |
| `pendingCreditAdjustments`      | 尚未開立發票的調整 (如退款)。  |
| `expiredCredit`      |  自上一張發票以來過期的點數。  |
| `pendingEligibleCharges`  | 尚未開立發票的適用點數費用。   |

### <a name="get-list-of-credits"></a>取得點數清單

提出下列要求，並以從第一個步驟複製的 `id` 取代 `<billingProfileId>` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)。 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
API 回應會傳回帳單設定檔的 Azure 點數清單。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| 元素名稱  | 說明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | 原始點數金額。 |
| `closedBalance`   | 截至最後一張發票為止的餘額。    |
| `source`      | 可定義取得點數之人員/方式的來源。 |
| `startDate`      |  點數生效的日期。  |
| `expirationDate`  | 點數到期的日期。   |
| `poNumber`  | 點數計費發票的採購單號碼。   |

### <a name="get-transactions-that-affected-credit-balance"></a>取得對點數餘額造成影響的交易

提出下列要求，並以從第一個步驟複製的 `id` 取代 `<billingProfileId>` (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)。 您需要傳遞 **startDate** 和 **endDate**，以取得所需持續時間的交易。

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
API 回應會傳回所有影響帳單設定檔之點數餘額的交易。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| 元素名稱  | 說明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | 交易發生的日期。 |
| `description` | 交易的描述。 |
| `adjustments`   | 交易的點數調整。    |
| `creditExpired`      | 已過期的點數金額。 |
| `charges`      |  交易的費用。  |
| `closedBalance`  | 交易後的餘額。   |
| `eventType`  | 交易的類型。   |
| `invoiceNumber`  | 交易計費發票的發票號碼。 暫止交易的的發票號碼會是空的。   |

## <a name="how-credits-are-used"></a>點數的使用方式

在 Microsoft 客戶合約的計費帳戶中，您可以使用帳單設定檔來管理發票和付款方式。 每個帳單設定檔都會按月產生發票，而您可以使用付款方式來支付發票費用。

您會將所取得的點數指派給帳單設定檔。 為帳單設定檔產生發票時，點數會自動套用至總費用，以計算您需要支付的金額。 您可以使用您的付款方式 (例如支票/電匯或信用卡) 來支付其餘金額。

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure 點數未涵蓋的產品

 您的 Azure 點數不涵蓋下列產品。 無論您有多少點數餘額，都必須支付這些產品的使用費用：

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- 已註冊的使用者
- Openlogic
- 遠端存取權限 XenApp Essentials 已註冊的使用者
- Ubuntu Advantage
- Visual Studio Enterprise (每月)
- Visual Studio Enterprise (每年)
- Visual Studio Professional (每月)
- Visual Studio Professional (每年)
- Azure Marketplace 產品
- Azure 支援方案

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查對 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [了解 Microsoft 客戶合約的計費帳戶](billing-mca-overview.md)
- [了解 Microsoft 客戶合約發票上的詞彙](billing-mca-understand-your-invoice.md)
