---
title: 預付的軟體計劃-Azure 保留項目
description: 了解如何預付軟體計劃，以省下較隨用隨付的成本。
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: c8e9d07bf01536d7865bd79f667a937037d96837
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67489888"
---
# <a name="prepay-for-azure-software-plans"></a>預付 Azure 軟體方案

當您預先支付 Azure 中您 SUSE 及 RedHat 軟體使用情況時，您可以隨用隨付成本節省金錢。 折扣僅適用於 SUSE 及 RedHat 的計量，而不是在虛擬機器使用情況。 您可以購買保留項目分開，進一步節省空間的虛擬機器。

您可以購買 Azure 入口網站中的 SUSE 及 RedHat 軟體方案。 若要購買方案：

- 您必須至少一個企業或隨用隨付定價的個別訂用帳戶的擁有者角色。
- Enterprise 訂用帳戶，如**新增保留的執行個體**必須在啟用選項[EA 入口網站](https://ea.azure.com/)。 如果設定已停用，您必須是訂用帳戶為 EA 系統管理員。
- 雲端解決方案提供者 (CSP) 計畫中，銷售的代理程式的管理專員可以購買軟體方案。

## <a name="buy-a-software-plan"></a>購買軟體方案

1. 登入 Azure 入口網站並移至[保留](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
2. 按一下 **新增**，然後選取您想要購買軟體方案。
填寫必要欄位。 所有 SUSE Linux VM 或 RedHat VM 符合您所購買的屬性都取得折扣。 取得折扣的實際部署數目取決於選取的範圍和數量。
3. 選取一個訂用帳戶。 它用來購買的方案。
訂用帳戶付款方法會負責保留項目的預付費用。 訂用帳戶類型必須是 Enterprise 合約 (提供數字：MS-AZR-0017P 或 MS-AZR-0148 P) 或個別的合約，與隨用隨付定價 (提供數字：MS-AZR-0003P 或 MS-AZR-0023P)。
    - 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
    - 隨用隨付定價的個別訂用帳戶，費用是訂用帳戶的信用卡或發票付款方法來計費。
4. 選取範圍。 此範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。
    - 單一訂用帳戶-計劃折扣會套用至對應的訂用帳戶中的使用方式。
    - （共用）-計劃折扣會套用至比對您的帳單寄送內容中的任何訂用帳戶中的執行個體。 適用於企業客戶，計費內容會是註冊，並包含所有訂用帳戶中註冊。 個別的計劃與隨用隨付定價的客戶，計費內容是由帳戶系統管理員建立的隨用隨付定價訂閱的所有個別計畫。
5. 選擇要選擇的 VM 大小和映像類型的產品。 選取 VM 大小僅適用於折扣。
6. 選取的一年期或三年的詞彙。
7. 選擇數量，也就是可以取得帳單折扣的預付 VM 執行個體數目。
8. 加入購物車、 檢閱及購買的產品。

保留折扣會自動套用到預先支付的軟體計量。 此計劃不涵蓋 VM 的計算費用。 您可以分開購買的 VM 保留項目。

## <a name="discount-applies-to-different-suse-vm-sizes"></a>折扣會套用至不同的 SUSE VM 大小

保留的 VM 執行個體，例如 SUSE Linux 計劃會提供執行個體大小的彈性。 您的折扣適用於您部署不同的大小，於您購買 SUSE 計劃的 VM 時，即使。 如需詳細資訊，請參閱 <<c0> [ 了解軟體計劃折扣如何套用](../../billing/billing-understand-suse-reservation-charges.md)。

## <a name="redhat-plan-discount"></a>RedHat 計劃折扣

計劃是僅適用於 Red Hat Enterprise Linux 虛擬機器。 折扣不適用於 RedHat Enterprise Linux 的 SAP HANA Vm 或 RedHat Enterprise Linux SAP 商務應用程式的 Vm。

RedHat 計劃折扣僅適用於您在購買時選取的 VM 大小。 無法退款或之後購買交換 RHEL 計劃。


## <a name="cancellation-and-exchanges-not-allowed"></a>不允許取消和交換

您無法取消，或交換您購買的 SUSE 或 RedHat 計劃。 檢查您的使用量，以確定您購買適當的方案。 若要找出要購買的項目說明，請參閱[了解軟體計劃折扣如何套用](../../billing/billing-understand-suse-reservation-charges.md)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](../../billing/billing-manage-reserved-vm-instance.md)。

若要深入了解，請參閱下列文章：

- [什麼是 Azure 保留項目？](../../billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 中的保留](../../billing/billing-manage-reserved-vm-instance.md)
- [了解 SUSE 保留折扣的套用方式](../../billing/billing-understand-suse-reservation-charges.md)
- [了解隨用隨付方案訂用帳戶的保留項目使用量](../../billing/billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../../billing/billing-understand-reserved-instance-usage-ea.md)
