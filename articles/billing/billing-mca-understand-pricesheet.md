---
title: 了解價位表中的條款，Microsoft 客戶合約-Azure
description: 了解如何閱讀並了解您的使用量和帳單，Microsoft 客戶協議。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490655"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>在您的 Microsoft 客戶合約價位表中的條款

這篇文章適用於 Azure 的計費帳戶的 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

如果您是擁有者、 參與者、 讀取器或發票管理員的帳單設定檔您可以從 Azure 入口網站來下載您組織的價位表。 請參閱[檢視並下載您的組織的定價](billing-ea-pricing.md)。

## <a name="terms-and-descriptions-in-your-price-sheet"></a>條款和價位表中的描述

下節說明您的 Microsoft 客戶合約價位表所示的重要字詞。

| **欄位名稱**   | **說明**   |
| --- | --- |
| billingAccountId  | 計費帳戶的唯一識別碼。   |
| billingAccountName  | 計費帳戶的名稱。  |
| billingProfileId  | 帳單的設定檔的唯一識別碼。   |
| billingProfileName  | 計費將設定為接收發票的設定檔的名稱。 價位表中的價格是此計費的設定檔相關聯。 |
| productOrderName  | 已購買的產品計劃的名稱。 |
| serviceFamily  | Azure 服務的型別。例如：計算、 分析、 安全性 |
| Products  | 因為而產生費用的產品名稱。例如：基本的 SQL DB、 標準的 SQL DB  |
| productId  | 其計量會取用產品的唯一識別碼。 |
| unitOfMeasure  | 識別服務計費的測量的單位。 例如，計算服務是按每小時計費。 |
| meterId  | 計量的唯一識別碼。 |
| meterName  | 計量的名稱。 計量代表一項 Azure 服務的可部署資源。 |
| meterCategory  | 計量的分類類別目錄的名稱。 例如，_雲端服務_，_網路_，依此類推。 |
| meterType  |  計量型別的名稱。 |
| meterSubCategory  | 計量子分類類別目錄的名稱。  |
| meterRegion  | 服務計量表可使用的區域名稱。 針對根據資料中心位置定價的某些服務，識別資料中心的位置。    |
| tierId  | 識別適用時的定價層。 這適用於搭配 tierMinimumUnits 時，設定分層式的價格價格不同基礎上耗用的單位數目。    |
| tierMinimumUnits  | 定義為其所定義的價格層範圍的下限。 例如，如果範圍是 0 到 100，tierMinimumUnits 會是 0。  |
| effectiveStartDate  | 開始價格生效的日期。 |
| effectiveEndDate  | 有效的價格的結束日期。 |
| unitPrice  | 每個單位計費 （不有效混合的價格） 時的價格為特定計量和產品的訂單名稱。  注意：單位價格不相同時有差異的價格層次的服務下載的使用方式詳細資料中有效的價格。  如果服務使用多層式定價，有效價格混合的速率層並不會顯示特定層的單價。 混合的價格或有效的價格是跨越多個階層 （其中每一層有特定的單價） 取用的數量的 net 價格。 |
| basePrice  | 市場價格次客戶登入，或市場價格當時服務計量會啟動後登入。   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [檢視及下載您的組織定價](billing-ea-pricing.md)
