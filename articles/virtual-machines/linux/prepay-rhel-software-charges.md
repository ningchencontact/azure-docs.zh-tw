---
title: 購買 Azure 保留的 Red Hat Enterprise Linux 計劃-|Microsoft Docs
description: 了解如何支付您的 Red Hat 使用量，並省下較隨用隨付的成本。
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
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652920"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>從 Azure 保留的 Red Hat Enterprise Linux 方案預付

支付您的 Red Hat 使用量，並省下較隨用隨付的成本。 折扣僅適用於 Red Hat 計量，而不是在虛擬機器使用情況。 您可以分開購買虛擬機器的保留，節省更多金錢。

您可以購買 Azure 入口網站中的 Red Hat 軟體方案。 若要購買方案：

- 您必須至少為一個企業或"隨用隨付"型方案訂用帳戶的「擁有者」角色。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用**新增保留執行個體**。 或者，如果該設定已停用，則您必須是訂用帳戶上的 EA 系統管理員。
- 雲端解決方案提供者 (CSP) 計畫中，銷售的代理程式的管理專員可以購買的 Red Hat 計劃。

## <a name="buy-a-red-hat-software-plan"></a>購買的 Red Hat 軟體計劃

1. 移至 Azure 入口網站中的 [Reservations](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)。
1. 選取 **新增**，然後選取 Red Hat Linux。
1. 填寫必要欄位。 任何符合您所購買的屬性的 Red Hat Linux VM 取得折扣。 取得折扣的實際部署數目取決於選取的範圍和數量。

    | 欄位      | 描述|
    |:------------|:--------------|
    |名稱        |這項購買的名稱。|
    |訂用帳戶|用來支付此方案的訂用帳戶。 保留項目的預付費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或預付型方案 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對預付型方案訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。|
    |影響範圍       |此範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： <ul><li>單一訂用帳戶-計劃折扣會套用到此訂用帳戶中的 Red Hat Linux 使用方式。 </li><li>（共用）-計劃折扣會套用至計費內容內的任何訂用帳戶中的 Red Hat Linux 使用方式。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶。 針對預付型方案客戶，共用範圍是帳戶系統管理員所建立的所有預付型方案訂用帳戶。</li></ul>|
    |規劃     |選取的 Red Hat Linux 方案。 協助識別要購買的項目，請參閱了解如何將 Red Hat Linux Enterprise 軟體保留折扣套用。|
    |VM 大小     |Red Hat Linux 定價取決於 VM 上的 Vcpu 數目。 選取代表您的 Red Hat Linux Vm 上的 Vcpu 數目的選項。|
    |詞彙        |一年或三年。|
    |數量    |規劃您買了此 Red Hat Linux 的 Vm 數目。 數量是執行可以取得帳單折扣的 Red Hat Linux 執行個體的數目。|
1. 選取 [購買]。
1. 選取 [檢視此保留] 以查看您的購買狀態。

保留折扣會自動套用至任何執行中 Red Hat 虛擬機器符合保留項目。 折扣僅適用於 Red Hat 計量。 此方案未涵蓋 VM 計算費用。

## <a name="discount-applies-to-different-vm-sizes"></a>折扣適用於不同 VM 大小

保留的 VM 執行個體，例如 Red Hat Linux 計劃會提供執行個體大小的彈性。 這表示您的折扣套用甚至是當您部署不同的大小，從您所購買的 Red Hat 計劃的 VM。 如需詳細資訊，請參閱了解如何將 Red Hat Linux Enterprise 軟體保留折扣套用。

## <a name="cancellation-and-exchanges-not-allowed"></a>不允許取消和交換

您無法取消，或交換您購買的 Red Hat 計劃。 檢查您的使用量，以確定您購買適當的方案。 協助識別要購買的項目，請參閱了解如何將 Red Hat Linux Enterprise 軟體保留折扣套用。

## <a name="next-steps"></a>後續步驟

若要了解如何管理保留項目，請參閱[管理 Azure 保留項目](../../billing/billing-manage-reserved-vm-instance.md)。

若要深入了解，請參閱下列文章：

- [什麼是 Azure 保留項目？](../../billing/billing-save-compute-costs-reservations.md)
- [管理 Azure 中的保留](../../billing/billing-manage-reserved-vm-instance.md)
- 了解如何將 Red Hat 保留折扣套用
- [了解隨用隨付方案訂用帳戶的保留項目使用量](../../billing/billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。