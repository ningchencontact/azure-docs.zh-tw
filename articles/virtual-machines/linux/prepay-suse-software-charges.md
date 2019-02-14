---
title: 購買 SUSE Linux 方案 - Azure Reservations | Microsoft Docs
description: 了解如何預付 SUSE 使用量，並節省預付型方案成本的金額。
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: 4f70a34febcf0b39d051053a6ddd9abe5c9a6726
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745974"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>從 Azure Reservations 預付 SUSE 軟體方案

預付 SUSE 使用量，並節省預付型方案成本的金額。 折扣僅適用於 SUSE 計量，但不適用於虛擬機器使用量。 您可以分開購買虛擬機器的保留，節省更多金錢。

您可以在 Azure 入口網站中購買 SUSE 軟體方案。 若要購買方案：

- 您必須至少為一個企業或"隨用隨付"型方案訂用帳戶的「擁有者」角色。
- 針對企業訂用帳戶，必須在[ EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
- 對於雲端解決方案提供者 (CSP) 方案，系統管理員代表或銷售代表都可以購買 SUSE 方案。

## <a name="buy-a-suse-software-plan"></a>購買 SUSE 軟體方案

1. 移至 Azure 入口網站中的 [Reservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
1. 選取 [新增]，然後選取 SUSE Linux。
1. 填寫必要欄位。 任何符合您所購買屬性的 SUSE Linux VM 均可取得折扣。 取得折扣的實際部署數目取決於選取的範圍和數量。

    | 欄位      | 說明|
    |:------------|:--------------|
    |Name        |這項購買的名稱。|
    |訂用帳戶|用來支付此方案的訂用帳戶。 保留項目的預付費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或預付型方案 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對預付型方案訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。|
    |影響範圍       |此範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： <ul><li>單一訂用帳戶 - 方案折扣會套用至此訂用帳戶中的 SUSE Linux 使用量。 </li><li>共用 - 此方案折扣會套用至計費內容內任何訂用帳戶中的 SUSE Linux 使用量。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對預付型方案客戶，共用範圍是帳戶系統管理員所建立的所有預付型方案訂用帳戶。</li></ul>|
    |軟體方案     |選取 SUSE Linux 方案。 如需識別所要購買項目的協助，請參閱[了解如何套用 SUSE Linux Enterprise 軟體保留折扣](../../billing/billing-understand-suse-reservation-charges.md)。|
    |VM 大小     |SUSE Linux 定價取決於 VM 上的 vCPU 數目。 選取代表 SUSE Linux VM 上 vCPU 數目的選項。|
    |詞彙        |一年或三年。|
    |數量    |您為其購買此 SUSE Linux 方案的 VM 數目。 數量是可以取得帳單折扣的執行中 SUSE Linux 執行個體數目。|
1. 選取 [購買]。
1. 選取 [檢視此保留] 以查看您的購買狀態。

保留折扣會自動套用到任何符合保留的執行中 SUSE 虛擬機器。 此折扣僅適用於 SUSE 計量。 此方案未涵蓋 VM 計算費用。

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>折扣適用於具有執行個體大小彈性的各種 VM 大小

如同保留的 VM 執行個體，SUSE Linux 方案可提供執行個體大小彈性。 這表示您的折扣甚至適用於當您從所購買的 SUSE 方案部署不同大小的 VM 時。 如需詳細資訊，請參閱[了解如何套用 SUSE Linux Enterprise 軟體保留折扣](../../billing/billing-understand-suse-reservation-charges.md)。

## <a name="cancellation-and-exchanges-not-allowed"></a>不允許取消和交換

您無法取消或交換您我購買的 SUSE 方案。 檢查您的使用量，以確定您購買適當的方案。 如需識別所要購買項目的協助，請參閱[了解如何套用 SUSE Linux Enterprise 軟體保留折扣](../../billing/billing-understand-suse-reservation-charges.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](../../billing/billing-manage-reserved-vm-instance.md)。

若要深入了解，請參閱下列文章：

- [什麼是 Azure 保留項目？](../../billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 中的保留](../../billing/billing-manage-reserved-vm-instance.md)
- [了解 SUSE 保留折扣的套用方式](../../billing/billing-understand-suse-reservation-charges.md)
- [了解隨用隨付方案訂用帳戶的保留項目使用量](../../billing/billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。