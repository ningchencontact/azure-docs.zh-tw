---
title: 檢視 Azure 資源的保留 | Microsoft Docs
description: 了解如何在 Azure 入口網站中檢視 Azure 保留。
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 872837c774368820527b12778b1a7dd4ddc5c7af
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995518"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>在 Azure 入口網站中檢視 Azure 保留的項目

根據您的訂用帳戶類型和權限，有幾種方式來檢視適用於 Azure 的保留項目。

## <a name="view-purchased-reservations"></a>檢視購買的保留項目

當您購買保留時，您和帳戶管理員預設可以檢視該保留。 您與帳戶管理員會自動取得保留訂單和保留項目擁有者角色。 若要允許其他人可以檢視此保留項目，您必須將其新增為**擁有者**或是**讀取器**保留順序或保留項目上。

如需詳細資訊，請參閱[新增或變更可以管理保留的使用者](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)。

若要以「擁有者」或「讀者」身分檢視保留，

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 針對 [保留] 進行搜尋。
    ![螢幕擷取畫面顯示 Azure 入口網站搜尋](./media/billing-view-reservation/portal-reservation-search.png)  
3. 此清單會顯示所有保留項目具有 「 擁有者 」 或 「 讀取者角色。 每個保留項目顯示的最後一個已知的使用率百分比。  
    ![範例，顯示一份保留項目](./media/billing-view-reservation/view-reservations.png)
4. 選取保留並查看過去五天的使用率趨勢。  
    ![範例顯示保留的使用趨勢](./media/billing-view-reservation/reservation-utilization.png)
5. 您也可以取得[保留使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)使用保留的執行個體使用情況 API 與[Microsoft Azure 耗用量 Insights Power BI 內容套件](/power-bi/service-connect-to-azure-consumption-insights)。

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

- [Azure 保留項目有哪些？](billing-save-compute-costs-reservations.md)
- [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)

購買服務方案：

- [預付 Cosmos DB 保留容量的費用](../cosmos-db/cosmos-db-reserved-capacity.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../sql-database/sql-database-reserved-capacity.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)

購買軟體方案：

- [預付的 Azure 保留項目從 Red Hat 軟體方案](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [從 Azure 保留預付 SUSE 軟體方案](../virtual-machines/linux/prepay-suse-software-charges.md)

了解使用方式：

- [了解隨用隨付方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
- [了解 CSP 訂用帳戶的保留使用量](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
