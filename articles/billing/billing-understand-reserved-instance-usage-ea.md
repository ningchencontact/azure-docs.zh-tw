---
title: 了解 Enterprise 的Azure Reservations 使用方式 | Microsoft Docs
description: 學習如何看懂使用量，以了解 Enterprise 註冊的 Azure 保留套用情形。
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: banders
ms.openlocfilehash: 0f29544890fe10f4914de393a4b153cfe393a2ec
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904296"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>了解 Enterprise 註冊的 Azure 保留使用量

使用[保留頁面](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)的 **ReservationId** 和 [EA 入口網站](https://ea.azure.com)的使用量檔案，來評估保留使用量。 您也可以在 [EA 入口網站 ](https://ea.azure.com) \(英文\) 的使用情況摘要區段中查看保留使用情況。

如果在預付型方案計費內容中購買保留，請參閱[了解預付型方案訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)。

## <a name="usage-for-reserved-virtual-machines-instances"></a>保留的虛擬機器執行個體使用量

針對下列幾個區段，假設您在美國東部區域執行 Standard_D1_v2 Windows VM，且保留資訊看起來會類似下表：

| 欄位 | 值 |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|數量 |1|
|SKU | 標準_D1|
|區域 | eastus |

已涵蓋虛擬機器的硬體部分，因為部署的虛擬機器符合保留屬性。 若要查看保留未涵蓋哪些 Windows 軟體，請參閱 [Azure 保留 VM 執行個體的 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)。

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>保留 VM 執行個體 CSV 檔案中的使用量

您可以從 Enterprise 入口網站下載 Enterprise 使用量 CSV 檔案。 在 CSV 檔案中，篩選**其他資訊**並輸入 **ReservationID**。 以下螢幕擷取畫面顯示和此保留相關的欄位：

![Azure 保留的 Enterprise 合約 (EA) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. [其他資訊] 欄位中的 **ReservationId** 代表套用至 VM 的保留。
2. **ConsumptionMeter** 是 VM 的計量識別碼。
3. [計量識別碼] 是 $0 成本的保留計量。 保留的 VM 執行個體會支付執行中 VM 的費用。
4. Standard_D1 是一種 vCPU VM，且是在沒有 Azure Hybrid Benefit 的情況下部署的 VM。 因此，這個計量涵蓋 Windows 軟體的額外費用。 若要尋找對應於 D 系列 1 核心 VM 的計量，請參閱 [Azure 保留 VM 執行個體的 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)。  如果您擁有 Azure Hybrid Benefit，就不會產生此額外費用。

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>SQL Database 與 Cosmos DB 保留容量保留項目的使用情況

下列各節使用 Azure SQL Database 作為範例來說明使用情況報表。 您也可以使用相同步驟來取得 Azure Cosmos DB 的使用情況。 

假設您目前在美國東部區域執行 SQL Database Gen 4，且保留資訊看起來類似下表：

| 欄位 | 值 |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|數量 |2|
|產品| SQL Database Gen 4 (2 核心)|
|區域 | eastus |

### <a name="usage-in-csv-file"></a>CSV 檔案中的使用方式 

針對 [其他資訊] 進行篩選並輸入您的 [保留識別碼]，然後選擇所需的 [計量類別目錄] - [Azure SQL Database] 或 [Azure Cosmos DB]。 以下螢幕擷取畫面顯示和此保留相關的欄位。

![SQL Database 保留容量的 Enterprise 合約 (EA) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. [其他資訊] 欄位中的 **ReservationId** 是套用至 SQL Database 資源的保留。
2. **ConsumptionMeter** 是 SQL Database 資源的計量識別碼。
3. [計量識別碼] 是 $0 成本的保留計量。 任何有資格獲得保留的 SQL Database 資源都會在 CSV 檔案中顯示此計量識別碼。

## <a name="usage-summary-page-in-enterprise-portal"></a>Enterprise 入口網站中的使用量摘要頁面

Azure 保留使用量也會顯示在企業版入口網站的 [使用量摘要] 區段中：![Enterprise 合約 (EA) 使用量摘要](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. 您不需要針對 VM 的硬體元件付費，原因是保留已涵蓋該費用。 針對 SQL Database 保留，您會看到有一行以**服務名稱**作為 Azure SQL Database 保留容量使用量。
2. 在此範例中，您沒有 Azure Hybrid Benefit，因此需要就 VM 所使用的 Windows 軟體支付費用。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../sql-database/sql-database-reserved-capacity.md) 
- [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
- [了解保留項目折扣的套用方式](billing-understand-vm-reservation-charges.md)
- [了解隨用隨付方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
- [Windows 軟體成本不包含在 Reservations 內](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

