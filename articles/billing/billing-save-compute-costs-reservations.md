---
title: 什麼是 Azure 保留執行個體？ | Microsoft Docs
description: 了解 Azure 保留的 VM 執行個體和 VM 定價，藉以降低虛擬機器成本和取得最具效益的價格。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 12fc88596b4283b6f809575328ab801704cc1c8d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064267"
---
# <a name="what-are-azure-reserved-vm-instances"></a>什麼是 Azure 保留的 VM 執行個體？
[Azure 保留的 VM 執行個體](https://azure.microsoft.com/pricing/reserved-vm-instances)可藉由讓您預先支付一年或三年的計算容量，以獲得所用虛擬機器的折扣，來協助您節省成本。 Azure 保留執行個體可以大幅降低虛擬機器成本，預付一或三年期承諾用量費用，即可節省高達預付型方案價格的 72%。 保留執行個體提供計費折扣，且不會影響虛擬機器的執行階段狀態。

您可以在 [Azure 入口網站](https://aka.ms/reservations)購買保留執行個體 (RI)。 如需詳細資訊，請參閱[預付虛擬機器並且使用保留執行個體來節省成本](https://go.microsoft.com/fwlink/?linkid=861721)。

## <a name="why-should-i-buy-a-reserved-instance"></a>為什麼應該購買保留執行個體？
如果您有長期執行的虛擬機器，則購買保留執行個體可提供您最符合成本效益的選項。 例如，如果您持續在美國西部區域執行四個標準 D2 VM 執行個體，在未使用保留執行個體的情況下，會以預付型方案費率計費。 如果您為這四個 VM 購買保留執行個體，這些 VM 就會立即享有計費優惠。 它們不再以隨用隨付費率計費。 

## <a name="what-charges-does-a-reserved-instance-cover"></a>保留執行個體涵蓋哪些費用？
保留執行個體僅涵蓋 Windows 或 Linux 虛擬機器的虛擬機器基礎結構費用。 保留執行個體並不涵蓋額外的軟體、網路或儲存體費用。 對於 Windows 虛擬機器，您可以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 以涵蓋 Windows 授權成本。

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>誰有資格購買保留執行個體？
凡是具有以下這些訂用帳戶類型的 Azure 客戶都可以購買保留執行個體：
-   Enterprise 合約訂用帳戶供應項目類型 (MS-AZR-0017P)。
-   
  [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶供應項目類型 (MS-AZR-003P)。 您必須具有訂用帳戶的「擁有者」角色，才能購買保留的執行個體。 如果要在 Enterprise 註冊中購買保留執行個體，企業系統管理員必須在 EA 入口網站中啟用保留執行個體購買功能。 此設定預設為啟用狀態。
-   雲端解決方案提供者 (CSP) 合作夥伴可以使用 Azure 入口網站或[合作夥伴中心](https://docs.microsoft.com/partner-center/azure-reservations)來購買保留執行個體。

## <a name="how-is-a-reserved-instance-purchase-billed"></a>保留執行個體購買如何計費？
保留執行個體購買費用會向與訂用帳戶繫結的付款方式收取。 如果您有 Enterprise 訂用帳戶，保留執行個體費用會從您的承諾用量餘額中扣除。 如果您的承諾用量餘額未涵蓋保留執行個體費用，就會向您收取超額部分的費用。
如果您有隨用隨付訂用帳戶，會對您的帳戶所登記的信用卡立即計費。 如果是透過發票計費，您會在下一張發票上看到費用。

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>如何套用已購買的保留執行個體折扣？
保留執行個體折扣適用於與您購買保留執行個體時所選屬性相符的虛擬機器。 屬性包含相符的 VM 執行所在的範圍。 例如，如果您想要美國西部區域四個標準 D2 虛擬機器的保留執行個體折扣，請選取這些 VM 執行所在的訂用帳戶。 如果虛擬機器是在註冊/帳戶內的不同訂用帳戶中執行，則選取共用的範圍。 共用範圍可讓您將保留執行個體折扣套用到所有訂用帳戶。 您可以在購買保留執行個體之後變更此範圍。 若要變更此範圍，請參閱[在 Azure 中管理保留執行個體](billing-manage-reserved-vm-instance.md)。

保留執行個體折扣僅適用於與企業或預付型方案訂用帳戶類型相關聯的虛擬機器。 在其他供應項目類型訂用帳戶中執行的虛擬機器不會獲得保留執行個體折扣。 針對 Enterprise 註冊，企業開發/測試訂用帳戶沒有資格獲得保留執行個體權益。

若要進一步了解保留執行個體如何影響虛擬機器計費，請參閱[了解如何套用保留執行個體折扣](billing-understand-vm-reservation-charges.md)。

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>保留執行個體期限到期時會發生什麼事？
在保留執行個體期限結束時，計費折扣會到期，虛擬機器基礎結構會以預付型方案價格計費。 Azure 保留執行個體不會自動更新。 若要繼續獲得計費折扣，您必須購買新的保留執行個體。 

## <a name="sizes-and-regional-availability"></a>大小和區域可用性
保留執行個體適用於大多數 VM 大小，但有些例外：
- 預覽版 VM – 任何預覽版 VM 系列或大小都不適用於保留執行個體購買。
- 雲端 – 保留執行個體不適用於 Azure 美國政府、德國或中國區域中的購買。 
- 配額不足 – 保留執行個體如果範圍限定在單一訂用帳戶，必須訂用帳戶中有可用的 vCPU 配額，才能新增 RI。 例如，如果目標訂用帳戶的配額限制為 10 個 D 系列的 vCPU，您便無法購買一個適用於 11 個 Standard_D1 執行個體的保留執行個體。 保留執行個體的配額檢查包括訂用帳戶中已經部署的 VM。 例如，如果訂用帳戶的配額為 10 個 D 系列的 vCPU，且已部署 2 個 standard_D1 執行個體，則您可以在此訂用帳戶中購買一個適用於 10 個 standard_D1 執行個體的保留執行個體。 
- 容量限制 – 在極少數的情況下，Azure 會因為區域中的容量較低，而限制購買一小部分 VM 大小的新保留執行個體。

## <a name="next-steps"></a>後續步驟
購買 [Azure 保留執行個體](https://go.microsoft.com/fwlink/?linkid=861721)，開始節省虛擬機器的成本。 

若要深入了解保留執行個體，請參閱下列文章：

- [什麼是 Azure 保留的 VM 執行個體？](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理 Azure 中的保留執行個體](billing-manage-reserved-vm-instance.md)
- [了解如何套用保留執行個體折扣](billing-understand-vm-reservation-charges.md)
- [了解預付型方案訂用帳戶的保留執行個體使用量](billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留執行個體使用量](billing-understand-reserved-instance-usage-ea.md)
- [Windows 軟體的成本不包括在保留執行個體內](billing-reserved-instance-windows-software-costs.md)
- [合作夥伴中心雲端解決方案提供者 (CSP) 程式中的保留執行個體](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
