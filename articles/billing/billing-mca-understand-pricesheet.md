---
title: 了解價位表中的條款，Microsoft 客戶合約-Azure |Microsoft Docs
description: 學習如何閱讀並了解 Azure 訂用帳戶的使用情形和帳單
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: eb6184e10d38cdcfad7070663e36f6610d009cdb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371354"
---
# <a name="understand-the-terms-in-your-price-sheet-for-a-microsoft-customer-agreement"></a>了解 Microsoft 的客戶合約的價位表中的條款

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

如果您是擁有者、 參與者、 讀取器或發票管理員的帳單設定檔您可以從 Azure 入口網站來下載您組織的價位表。 請參閱[檢視並下載您的組織的定價](billing-ea-pricing.md)。

## <a name="detailed-terms-and-descriptions-in-your-microsoft-customer-agreement-price-sheet"></a>詳細的字詞和描述，在您的 Microsoft 客戶合約價位表

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

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- [檢視及下載您的組織定價](billing-ea-pricing.md)
