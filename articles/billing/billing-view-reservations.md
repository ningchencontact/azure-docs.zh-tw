---
title: 檢視 Azure 資源的保留 | Microsoft Docs
description: 了解如何在 Azure 入口網站中檢視 Azure 保留。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2018
ms.author: cwatson
ms.openlocfilehash: c7522076987aacacc6fde6a0c9d2fa867a3f14aa
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314035"
---
# <a name="view-reservations-for-azure-in-the-azure-portal"></a>在 Azure 入口網站中檢視 Azure 的保留

視您的訂用帳戶類型和權限而定，有一些方式可檢視 Azure 資源的保留。

## <a name="view-reservations-as-owner-or-reader"></a>以擁有者或讀取者身分檢視保留

當您購買保留時，您和帳戶管理員預設可以檢視該保留。 您和帳戶管理員會自動取得該保留的「擁有者」角色。 若要允許其他人員檢視保留，您必須將他們新增為該保留的「擁有者」或「讀者」。 如需詳細資訊，請參閱[新增或變更可以管理保留的使用者](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。
 
若要以「擁有者」或「讀者」身分檢視保留，

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [保留] 進行搜尋。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-view-reservation/portal-reservation-search.png)

1. 您會看到您具備「擁有者」或「讀者」角色的保留清單。

如果您需要變更保留的範圍、分割保留或變更可管理保留的人員，請參閱 [管理 Azure Reservations](billing-manage-reserved-vm-instance.md)。

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>檢視 Enterprise 註冊的保留交易

 如果您有合作夥伴主導的 Enterprise 註冊，請移至 EA 入口網站中的 [報告] 來檢視保留。 針對其他 Enterprise 註冊，您可以在 EA 入口網站及 Azure 入口網站中檢視保留。 您必須是 EA 系統管理員，才能檢視保留交易。

若要在 Azure 入口網站中檢視保留交易，

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單] 進行搜尋。

    ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-view-reservation/portal-cm-billing-search.png)

1. 選取 [保留交易]。
1. 若要篩選結果，請選取 [時間範圍]、[類型] 或 [描述]。
1. 選取 [套用] 。

    ![顯示「保留交易」結果的螢幕擷取畫面](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

若要使用 API 來取得資料，請參閱[取得企業客戶的保留執行個體交易費用](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
- [預付 Cosmos DB 保留容量的費用](../cosmos-db/cosmos-db-reserved-capacity.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../sql-database/sql-database-reserved-capacity.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理 Azure 保留](billing-manage-reserved-vm-instance.md)
- [了解預付型方案訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
- [了解 CSP 訂用帳戶的保留使用量](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
