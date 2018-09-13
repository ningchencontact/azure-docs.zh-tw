---
title: 什麼是 Azure 保留項目？ | Microsoft Docs
description: 了解 Azure 保留和定價以節省虛擬機器、SQL 資料庫及其他資源的成本。
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
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 82b23f46acc94fefccc871583657200b90a33f05
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2018
ms.locfileid: "43303557"
---
# <a name="what-are-azure-reservations"></a>什麼是 Azure 保留項目？

Azure 保留可協助您透過預先支付一年或三年期的虛擬機器或 SQL 資料庫計算容量或其他 Azure 資源來節省成本。 預先支付費用可讓您在所使用的資源上取得折扣。 對於隨用隨付方案，保留可以大幅減少 72% 的虛擬機器、SQL 資料庫計算或其他資源成本。 保留會提供計費折扣，且不會影響資源的執行階段狀態。

您可以在 [Azure 入口網站](https://aka.ms/reservations)購買保留。 如需詳細資訊，請參閱下列主題：

- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>為什麼應該購買保留？

如果您有長期執行的虛擬機器或 SQL 資料庫，則購買保留可提供您最符合成本效益的選項。 例如，如果您持續在未使用保留的情況下執行某個服務的四個執行個體，便必須以隨用隨付方案費率支付費用。 如果您針對那些資源購買保留，便能立即取得保留折扣。 那些資源將不再以隨用隨付方案費率計費。

## <a name="what-charges-does-a-reservation-cover"></a>保留有涵蓋哪些費用？

- 保留的虛擬機器執行個體：保留僅涵蓋虛擬機器計算成本。 它不會涵蓋其他軟體、網路或儲存體費用。
- SQL Database 保留的 vCore：保留僅包含計算成本。 授權為分開計費。

針對 Windows 虛擬機器和 SQL 資料庫，您可以利用 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 來涵蓋授權成本。

## <a name="whos-eligible-to-purchase-a-reservation"></a>誰有資格購買保留？

凡是具有以下這些訂用帳戶類型的 Azure 客戶都可以購買保留：

- Enterprise 合約訂用帳戶供應項目類型 (MS-AZR-0017P)。
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)訂用帳戶供應項目類型 (MS-AZR-003P)。 您必須具有訂用帳戶的「擁有者」角色才能購買保留。
- 雲端解決方案提供者 (CSP) 合作夥伴可以使用 Azure 入口網站或[合作夥伴中心](https://docs.microsoft.com/partner-center/azure-reservations)來購買 Azure 保留。

保留折扣僅適用於與 Enterprise、隨用隨付方案，或 CSP 訂用帳戶類型相關聯的資源。

## <a name="how-is-a-reservation-billed"></a>保留的計費方式為何？

保留會計費至與訂用帳戶繫結的付款方式。 如果您有 Enterprise 訂用帳戶，保留成本是從您的預付餘額扣除。 如果您的預付餘額未涵蓋保留的成本，您會支付超額部分。 如果您有隨用隨付訂用帳戶，會對您的帳戶所登記的信用卡立即計費。 如果是透過發票計費，您會在下一張發票上看到費用。

## <a name="how-is-the-reservation-discount-applied"></a>保留折扣的套用方式為何？

保留折扣會套用至符合您於購買保留時所選取之屬性的資源使用量。 屬性包含相符的 VM、SQL 資料庫或其他資源所執行的範圍。 例如，如果您想要針對美國西部區域的四個標準 D2 虛擬機器取得保留折扣，請選取這些 VM 執行所在的訂用帳戶。 如果虛擬機器是在註冊/帳戶內的不同訂用帳戶中執行，則選取共用的範圍。 共用的範圍可讓保留折扣套用到整個訂用帳戶。 您可以在購買保留之後變更範圍。 如需詳細資訊，請參閱[管理 Azure 保留](billing-manage-reserved-vm-instance.md)。

保留折扣僅適用於與 Enterprise、隨用隨付方案，或 CSP 訂用帳戶類型相關聯的資源。 在具有其他優惠類型的訂用帳戶中執行的資源，將無法收到保留折扣。 針對 Enterprise 註冊，企業開發/測試訂用帳戶不具備獲得保留權益的資格。

若要進一步了解保留如何影響您的帳單，請參閱下列主題：

-  [了解 Azure 保留的虛擬機器執行個體折扣](billing-understand-vm-reservation-charges.md)
- [了解 Azure 保留折扣](billing-understand-vm-reservation-charges.md)
- 了解 SUSE 的 Azure 保留折扣和使用方式

## <a name="what-happens-when-the-reservation-term-expires"></a>保留期限到期時會發生什麼事？

在保留期限結束時，計費折扣會到期，而虛擬機器、SQL 資料庫或其他資源將會改以隨用隨付方案費率計費。 Azure 保留並不會自動更新。 若要繼續獲得計費折扣，您必須針對符合資格的服務和軟體購買新的保留。

## <a name="next-steps"></a>後續步驟

購買[保留的 VM 執行個體](../virtual-machines/windows/prepay-reserved-vm-instances.md)或 [SQL Database 保留容量](../sql-database/sql-database-reserved-capacity.md)，以開始節省虛擬機器的成本。

若要深入了解 Azure 保留項目，請參閱下列文章：

- [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
- [了解隨用隨付方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
- [Windows 軟體的成本不包括在保留項目內](billing-reserved-instance-windows-software-costs.md)
- [合作夥伴中心雲端解決方案提供者 (CSP) 計畫中的 Azure 保留項目](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
