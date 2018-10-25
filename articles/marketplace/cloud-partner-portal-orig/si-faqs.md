---
title: Seller Insights 常見問題集 | Microsoft Docs
description: Cloud Partner 入口網站 Seller Insights 功能的常見問題集。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: ec7fc3a7877cf0bffac0043a74c34d6f0f04826b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806673"
---
<a name="seller-insights-faq"></a>Seller Insights 常見問題集
===================

此文章針對 Seller Insights 內的常見使用者程序和相關問題提供指導方針。


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>在已下載的交易檔中尋找值的定義
------------------------------------------------------------------

交易檔案中計量值的定義將在 [Seller Insights 定義](./si-insights-definitions-v4.md)一文中找到。


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>查看已付費交易的客戶詳細資料
-------------------------------------------------------------

從「付款」模組下載您的交易之後，找出標示為 [付款狀態] 的資料行並套用篩選，就只會顯示「已付款」值。 下列資料行將會出現，其中包含客戶的詳細資料：[公司名稱]、[客戶電子郵件]、[客戶國家/地區]、[客戶狀態]，以及 [客戶郵遞區號]。


<a name="calculate-my-open-accounts-receivable"></a>計算我開立的應收帳款
-------------------------------------

從「付款」模組下載您的交易之後，找出標示為 [付款狀態] 的資料行並套用篩選，就只會顯示「即將付款」和「尚未準備付款」值。 接著，加總標示為 [付款金額 (PC)] 的資料行。


<a name="calculate-revenue-by-customer-usage-period"></a>計算客戶使用期間的營收
------------------------------------------

從「付款」模組下載您的交易之後，找出標示為 [交易狀態] 的資料行，並篩選「已付款」值。   針對列出的每一筆交易，標示為 [付款金額 (PC)] 的資料行代表您已支付的金額。  若要估計與交易相關聯的使用期間，請使用 [收費日期] 資料行，也就是交易適用期間最後一天使用的近似值。


<a name="calculate-your-bad-debt"></a>計算您的呆帳
---------------------

從「付款」模組下載您的交易之後，找出標示為 [最終收取狀態] 的資料行並套用篩選，就只會顯示「註銷」值。 接著，加總標示為 [付款金額 (PC)] 的資料行。


<a name="view-payout-or-customer-contact-information"></a>檢視付款或客戶連絡資訊
-------------------------------------------

使用具有「擁有者」角色，而非「參與者」角色的使用者身分登入。 只有擁有者角色會看到支出和客戶資訊。 您可以深入了解[管理使用者](./cloud-partner-portal-manage-users.md)一文中的使用者角色。


<a name="calculate-my-advance-payouts"></a>計算我的預付款
----------------------------

從「付款」模組下載您的交易之後，找出標示為 [交易類型] 的資料行並套用篩選，就只會顯示「費用」值。 接著，找出標示為 [最終收取狀態] 的資料行並套用篩選，就只會顯示「進行中」值。 最後，加總 [付款金額 (PC)] 資料行，以計算向客戶收取前支付給您的所有預付款。


<a name="calculate-customer-refunds"></a>計算客戶退款
--------------------------

從「付款」模組下載您的交易之後，找出標示為 [最終收取狀態] 的資料行並套用篩選，就只會顯示「退款」值。 加總 [費用金額 (PC)] 資料行，以計算為您的客戶處理的所有退款。


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>找出涉及 Microsoft 通路合作夥伴的交易
----------------------------------------------------------------

在 [Azure 授權類型] 資料行中經過篩選而顯示「企業到轉銷商」和「雲端解決方案提供者」的所有交易，均涉及 Microsoft 通路合作夥伴。 如需有關合作夥伴的更多詳細資訊，您可以在「付款」模組下載項目和「客戶」模組下載項目中，找到**轉售商名稱**和**轉銷商電子郵件**。


<a name="identify-trial-usage-and-trial-conversions"></a>找出試用版使用情況和試用版轉換
------------------------------------------

「訂單」、「使用情況」以及「付款」模組下載項目現在包含 [試用版結束日期]，可協助您了解該特定訂單的試用期結束時間 (如果適用)。 若要查看試用版使用情況和訂單，請找出下載項目中的 [SKU 計費類型] 資料行並套用篩選，就只會顯示「試用版」值。 若要查看試用版轉換，請找出下載項目中的 [試用版結束日期] 資料行並套用篩選，就只會在 [試用版結束日期] 超過今天的日期且 [取消日期] 資料行呈空白，或晚於 [試用版結束日期] 時顯示訂單。


<a name="when-is-my-monthly-payout-calculated"></a>什麼時候計算我的每月付款
------------------------------------

您的付款將在每個月的 15 日之前發放給您，其中包含上個月最後一天之前準備好支付的所有金額。 Microsoft 將會在每個月的 3 號，計算上一個月的付款金額，並將您的下載項目中所有適用的收費交易更新為 [付款狀態] 資料行中的「即將付款」。 這些交易將維持在該狀態，直到付款要求傳送到您的銀行帳戶為止，在這段期間內，其 [付款狀態] 將會更新為「已付款」，而且「付款日期」將會更新以顯示我們將付款要求提交到銀行的日期。


<a name="calculate-customer-acquisition-and-loss"></a>計算客戶獲取和損失
---------------------------------------

在客戶下載項目中找出 [取得日期] 資料行，就可以看到客戶第一次購買您的其中一個供應項目的日期。 同樣地，在客戶下載項目中找出 [損失日期] 資料行，就可以看到它們不再有您所發佈之供應項目的日期。


<a name="finding-more-help"></a>尋找其他協助
-----------------

- [Seller Insights 定義](./si-insights-definitions-v4.md) - 尋找計量和資料的定義

- [開始使用 Seller Insights](./si-getting-started.md) - Seller Insights 功能的簡介。

