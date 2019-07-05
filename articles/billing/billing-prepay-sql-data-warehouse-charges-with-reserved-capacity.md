---
title: 以 Azure 保留的容量預先支付 SQL 資料倉儲的費用
description: 了解如何您可以預先支付 SQL 資料倉儲的費用與保留的容量，以節省成本。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: banders
ms.openlocfilehash: cea2c8e6d476c3ea2799337ab2da1f9406731814
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565351"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>支付 SQL 資料倉儲的費用，以保留容量

您可以 prepaying cDWU 使用量的一或三年的持續時間內，使用 Azure SQL 資料倉儲節省成本。 若要購買保留的 SQL 資料倉儲容量，您必須選擇 Azure 區域和詞彙。 然後，將 SQL 資料倉儲 SKU 新增至購物車，並選擇您想要購買的 cDWU 單位數量。

當您購買保留時，SQL 資料倉儲符合保留屬性的使用方式不會再收費-付移費率。

保留未涵蓋儲存體或 SQL 資料倉儲的使用方式相關聯的網路費用。

保留的容量過期時，SQL 資料倉儲執行個體繼續執行，但以-付移費率收費。 保留項目不會自動更新。

如需定價資訊，請參閱[SQL 資料倉儲保留容量供應項目](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。

您可以購買保留的 Azure SQL 資料倉儲容量[Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。 若要購買保留容量：

- 您必須至少一個企業或隨用隨付訂用帳戶的擁有者角色。
- Enterprise 訂用帳戶，如**新增保留的執行個體**必須在啟用選項[EA 入口網站](https://ea.azure.com/)。 如果設定已停用，您必須是為 EA 系統管理員。
- 雲端解決方案提供者 (CSP) 計畫中，只有管理專員或銷售的代理程式都可以購買保留的 SQL 資料倉儲容量。

如需有關如何計費的保留項目購買的企業客戶和隨用隨付客戶的詳細資訊，請參閱[了解 Enterprise 註冊的 Azure 保留使用情況](billing-understand-reserved-instance-usage-ea.md)和[了解 Azure隨用隨付訂用帳戶的保留使用情況](billing-understand-reserved-instance-usage.md)。

## <a name="choose-the-right-size-before-purchase"></a>選擇正確的大小，購買之前

SQL 資料倉儲保留大小應該根據總計會計算您所使用的資料倉儲單位 (cDWU)。 購買項目進行 100 cDWU 增量。

例如，假設 SQL 資料倉儲的總耗用量是 DW3000c。 您想要購買的它所有的保留的容量。 因此，您應該購買 30 個 cDWU 保留容量。

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>購買保留的 SQL 資料倉儲容量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [所有服務]   > [保留]  。
3. 選取一個訂用帳戶。 您可以使用訂用帳戶清單來選擇用來針對所保留的容量付費的訂用帳戶。 訂用帳戶的付款方法會負責的保留容量的前期成本。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)。
  - 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
  - 針對隨用隨付訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。
4. 選取範圍。 您可以使用範圍清單來選擇訂用帳戶範圍。
  - **單一資源群組範圍**— 在選取的資源群組中的比對資源套用保留折扣。
  - **單一訂用帳戶範圍**— 保留折扣會套用至選取的訂用帳戶中比對資源。
  - **共用範圍**— 會保留折扣套用至對應的計費內容中的合格訂用帳戶中的資源。 Enterprise 合約客戶，計費內容會是註冊。 以隨用隨付費率的個別訂用帳戶，計費的範圍是由帳戶系統管理員建立的所有合格訂閱。
    - 適用於企業客戶計費的內容會是 EA 註冊。
    - 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。
5. 選取要選擇 Azure 區域的保留容量所涵蓋的區域。
6. 選擇 [數量]。 請輸入 100 的資料倉儲單位 (cDWU) 您想要購買的數量。    
  比方說，數量為 30 可讓您保留容量的 3000 cDWU 每隔一小時。
7. 檢閱 SQL 資料倉儲保留容量保留成本**成本**一節。
8. 選取 [購買]  。
9. 選取 **檢視此保留**以查看您的購買狀態。

## <a name="cancellations-and-exchanges"></a>取消和交換

如果您需要取消您的 SQL 資料倉儲保留容量，可能會有 12%提前解約金。 退款以您購買價格或目前保留價格的最低價格為準。 退費記錄僅限於美金 50,000.00 每年。 您會收到退款是按比例計算的餘額扣除 12%的提前解約金。 若要要求取消，請移至 Azure 入口網站中的保留，並選取 [退款]  以建立支援要求。

如果您需要將您的 SQL 資料倉儲保留容量變更為另一個區域或詞彙，可以將它交換另一個保留項目相等或更高的值。 新保留的期限開始日期不會延續自交換的保留。 一或三年期開始，當您建立新的保留項目。 若要要求交換，此保留項目中開啟 Azure 入口網站中，然後選取**Exchange**建立支援要求。

如需如何交換或退款的保留區的詳細資訊，請參閱[保留交換記錄和退費記錄](billing-azure-reservations-self-service-exchange-and-refund.md)。

保留折扣會自動套用到符合的 SQL 資料倉儲保留容量範圍和區域的 SQL 資料倉儲執行個體數目。 您可以更新的 SQL 資料倉儲保留容量範圍[Azure 入口網站](https://portal.azure.com/)、 PowerShell、 CLI 或 API。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何保留折扣套用到 Azure SQL 資料倉儲，請參閱[保留折扣如何套用到 Azure SQL 資料倉儲](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)。

- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
  - [了解 Azure 保留折扣](billing-understand-reservation-charges.md)
  - [了解隨用隨付方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
  - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
