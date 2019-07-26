---
title: 使用 Azure 保留容量預付 SQL 資料倉儲費用
description: 瞭解如何使用保留容量預付 SQL 資料倉儲費用來節省成本。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: banders
ms.openlocfilehash: 08c9f958f5103da9961f4c2d29be97f455cecc4b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359259"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>預付保留容量的 SQL 資料倉儲費用

您可以在一或三年期的期間內, 使用 Azure SQL 資料倉儲還要划算, 讓您的 cDWU 使用量節省成本。 若要購買 SQL 資料倉儲保留容量, 您需要選擇 Azure 區域和條款。 然後, 將 SQL 資料倉儲 SKU 新增至您的購物車, 並選擇您想要購買的 cDWU 單位數量。

當您購買保留時, 符合保留屬性的 SQL 資料倉儲使用量不會再以隨用隨付費率計費。

保留專案不會涵蓋與 SQL 資料倉儲使用量相關聯的儲存體或網路費用。

當保留容量到期時, SQL 資料倉儲實例會繼續執行, 但會以隨用隨付費率計費。 保留不會自動更新。

如需定價資訊, 請參閱[SQL 資料倉儲保留容量](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)供應專案。

您可以在[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)中購買 Azure SQL 資料倉儲保留容量。 若要購買保留容量：

- 您必須至少擁有一個企業或隨用隨付訂用帳戶的擁有者角色。
- 針對企業訂用帳戶, 必須在[EA 入口網站](https://ea.azure.com/)中啟用 [**新增保留實例**] 選項。 如果設定為停用, 您必須是 EA 系統管理員。
- 針對雲端解決方案提供者 (CSP) 計畫, 只有系統管理員代理程式或銷售代理人可以 SQL 資料倉儲保留容量購買。

如需企業客戶和隨用隨付客戶如何支付保留購買費用的詳細資訊, 請參閱[瞭解 enterprise 註冊的 azure 保留使用量](billing-understand-reserved-instance-usage-ea.md), 並[瞭解您的 azure 保留使用量隨用隨付訂用](billing-understand-reserved-instance-usage.md)帳戶。

## <a name="choose-the-right-size-before-purchase"></a>在購買前選擇適當的大小

SQL 資料倉儲保留大小應該以您取用的總計算資料倉儲單位 (cDWU) 為基礎。 購買是以 100 cDWU 的增量進行。

例如, 假設 SQL 資料倉儲的總耗用量是 DW3000c。 您想要為所有 it 購買保留容量。 因此, 您應該購買30個單位的 cDWU 保留容量。

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>購買 SQL 資料倉儲保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [所有服務] > [保留]。
3. 選取訂用帳戶。 使用 [訂用帳戶] 清單選擇用來支付保留容量的訂用帳戶。 訂用帳戶的付款方法會收取保留容量的預付成本。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)。
   - 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
   - 針對隨用隨付訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。
4. 選取範圍。 使用 [範圍] 清單來選擇訂用帳戶範圍。
   - **單一資源群組範圍**—只會將保留折扣套用至所選資源群組中的相符資源。
   - **單一訂**用帳戶範圍-將保留折扣套用至所選訂用帳戶中的相符資源。
   - **共用範圍**-將保留折扣套用至帳單內容中合格訂用帳戶的相符資源。 針對 Enterprise 合約客戶, 計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶, 計費範圍是由帳戶管理員所建立的所有合格訂用帳戶。
   - 若為企業客戶, 計費內容為 EA 註冊。
   - 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。
5. 選取區域, 以選擇保留容量所涵蓋的 Azure 區域。
6. 選擇數量。 輸入您想要購買的100資料倉儲單位 (cDWU) 數量。    
   例如, 數量30會每小時提供您3000的保留容量 cDWU。
7. 在 [**成本**] 區段中, 檢查 SQL 資料倉儲保留容量保留成本。
8. 選取 [購買]。
9. 選取 [**查看此保留**] 以查看您的購買狀態。

## <a name="cancellations-and-exchanges"></a>取消和交換

如果您需要取消 SQL 資料倉儲保留容量, 可能會有 12% 的提前終止費用。 退款以您購買價格或目前保留價格的最低價格為準。 每年的退款限制為 $50000.00。 您收到的退款是剩餘的按比例餘額減去 12% 的提前終止費用。 若要取消, 請移至 Azure 入口網站中的保留區, 然後選取 [**退款**]。

如果您需要將 SQL 資料倉儲保留容量變更為另一個區域或期限, 您可以將其交換為等於或大於價值的另一個保留。 新保留的期限開始日期不會延續自交換的保留。 當您建立新的保留時, 一或三年期就會開始。 若要交換, 請開啟 Azure 入口網站中的保留區, 然後選取 [ **exchange**]。

如需有關如何交換或退款保留的詳細資訊, 請參閱[保留交換和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

保留折扣會自動套用至符合 SQL 資料倉儲保留容量範圍和區域的 SQL 資料倉儲實例數目。 您可以使用[Azure 入口網站](https://portal.azure.com/)、POWERSHELL、CLI 或 API 來更新 SQL 資料倉儲保留容量的範圍。

## <a name="need-help-contact-us"></a>需要協助嗎? 請與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/)。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解保留折扣如何套用至 Azure SQL 資料倉儲, 請參閱[保留折扣適用于 Azure SQL 資料倉儲](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)。

- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
  - [了解 Azure 保留折扣](billing-understand-reservation-charges.md)
  - [了解隨用隨付訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)
  - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
