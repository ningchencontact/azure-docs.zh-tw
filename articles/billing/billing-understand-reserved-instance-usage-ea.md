---
title: 了解適用於 Enterprise 的 Azure 保留執行個體使用情況 - Azure 計費| Microsoft Docs
description: 了解如何讀取您的使用量，以了解 Enterprise 註冊的 Azure 保留 VM 執行個體如何應用。
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
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301309"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>了解 Enterprise 註冊的 Azure 保留執行個體使用量
從[保留頁面](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)使用 **ReservationId**，以及從 [EA 入口網站](https://ea.azure.com)使用使用量檔案，了解保留執行個體的使用情況。 您也可以在 [EA 入口網站](https://ea.azure.com)的使用情況摘要區段中查看保留執行個體使用情況。

>[!NOTE]
>如果在預付型方案計費內容中購買保留執行個體，請查看[了解您預付型方案訂用帳戶的保留執行個體使用情況。](billing-understand-reserved-instance-usage.md)

針對下一節，假設您在美國東部地區執行 Standard_D1_v2 Windows 虛擬機器，且保留執行個體資訊看起來像下面的表格：

| 欄位 | 值 |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|數量 |1|
|SKU | 標準_D1|
|區域 | eastus |

## <a name="reserved-instance-application"></a>保留執行個體的應用

已涵蓋虛擬機器的硬體部分，因為部署的虛擬機器符合保留執行個體屬性。 若要查看保留的執行個體未涵蓋哪些 Windows 軟體，請前往 Azure 保留的 VM 執行個體軟體成本，前往 [Azure 保留 VM 執行個體 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)。


### <a name="reserved-instance-usage-in-csv"></a>CSV 檔案中的保留執行個體使用狀況
您可以從 EA 入口網站下載 EA 使用情況 csv 檔案。 在下載的 csv 檔案中，篩選其他資訊並輸入您的**保留 ID**。 以下螢幕擷取畫面顯示和此保留執行個體相關的欄位：

![Azure 保留執行個體的 Enterprise 合約 (EA) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. [其他資訊] 欄位中的 **ReservationId** 代表用來將權益套用至虛擬機器的保留執行個體。
2. ConsumptionMeter 是 VM 的 MeterId。
3. 這是成本為 $0 的 ReservationMeter，因為執行中 VM 的成本已經由保留執行個體支付。 
4. Standard_D1 是一種 vCPU VM，且是在沒有 Azure Hybrid Benefit 的情況下部署的 VM。 因此，這個計量涵蓋 Windows 軟體的額外費用。 請參閱 [Azure 保留執行個體的 Windows 軟體成本](billing-reserved-instance-windows-software-costs.md)， 以尋找與 D 系列 1 核心 VM 相對應的計量。 如果使用 Azure Hybrid Benefit，將不會產生此額外費用。

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>EA 入口網站中使用情況摘要頁面中的保留執行個體使用情況

EA 入口網站的使用情況摘要區段 (![Enterprise 合約 (EA) 使用情況摘要](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)) 中也會顯示保留執行個體使用情況

1. 您不需要針對 VM 的硬體元件付費，因為保留執行個體中已涵蓋該費用。 
2. 但您需要針對 Windows 軟體付費，因為沒有使用 Azure Hybrid Benefit。 

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 保留執行個體，請參閱下列文章：

- [使用 Azure 保留執行個體以節省虛擬機器的成本](billing-save-compute-costs-reservations.md)
- [預付具有保留執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理保留執行個體](billing-manage-reserved-vm-instance.md)
- [了解保留執行個體折扣如何套用](billing-understand-vm-reservation-charges.md)
- [了解預付型方案的保留執行個體使用量](billing-understand-reserved-instance-usage.md)
- [Windows 軟體的成本不包括在保留的執行個體內](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。