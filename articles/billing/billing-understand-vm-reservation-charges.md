---
title: 了解 Azure 保留執行個體折扣 - Azure 計費 | Microsoft Docs
description: 了解如何將 Azure 保留虛擬機器執行個體的折扣，套用至執行中的虛擬機器。
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
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: a0800bafc3d6b858387e28a3b75bc7b3a6bfe6e8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301408"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>了解如何套用保留執行個體折扣
購買 Azure 保留虛擬機器執行個體之後，Azure 保留執行個體折扣就會自動套用至符合保留執行個體屬性和數量的虛擬機器上。 保留執行個體涵蓋您虛擬機器的基礎結構成本。 下表說明您購買保留執行個體後的虛擬機器成本。 在所有情況下，您將依標準費率支付儲存體和網路功能的費用。

| 虛擬機器類型  | 保留執行個體的費用 |    
|-----------------------|--------------------------------------------|
|沒有包含其他軟體的 Linux 虛擬機器 | 保留執行個體涵蓋了您虛擬機器的基礎結構成本。|
|包含軟體 (例如 Red Hat) 費用的 Linux 虛擬機器 | 保留執行個體涵蓋了您的基礎結構成本。 您必須支付額外的軟體費用。|
|沒有包含其他軟體的 Windows 虛擬機器 |保留執行個體涵蓋了您的基礎結構成本。 您必須支付 Windows 軟體的費用。|
|包含其他軟體 (例如 SQL server) 的 Windows 虛擬機器 | 保留執行個體涵蓋了您的基礎結構成本。 您必須支付 Windows 軟體和其他軟體的費用。|
|具有 [Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) 的 Windows 虛擬機器 | 保留執行個體涵蓋了您的基礎結構成本。 Azure Hybrid Benefit 所涵蓋的 Windows 軟體成本。 任何額外的軟體都是分開收費。| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>非 Windows 虛擬機器的保留執行個體折扣應用
 保留執行個體折扣會套用到執行的虛擬機器執行個體 (以小時計算)。 您購買的保留執行個體會與執行中虛擬機器發出的使用量進行比對，以便套用保留執行個體折扣。 對於未執行滿一整個小時的虛擬機器，會以其他未使用保留執行個體的虛擬機器 (包括目前執行的虛擬機器) 來計算保留執行個體數。 在一個小時結束時，將鎖定該小時內的虛擬機器保留執行個體應用。 如果虛擬機器未執行滿一整個小時，或在一小時內同時執行的虛擬機器數未達保留執行個體的小時數，則表示該小時內的保留執行個體使用量過低。 下圖說明可計費虛擬機器使用量的保留執行個體應用。 圖例是以一個購買的保留執行個體和兩個對應的虛擬機器執行個體為基礎。

![一個套用的保留執行個體和兩個對應虛擬機器執行個體的螢幕擷取畫面](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  保留執行個體線以上的任何使用量，都會依一般預付型方案的費率進行收費。 您不用支付保留執行個體這一條線下方的費用，因為此費用在購買保留執行個體時已涵蓋在內。
2.  在第一個 1 小時內，執行個體 1 執行了 0.75 個小時，而執行個體 2 執行了 0.5 小時。 第一個 1 小時的使用量總計為 1.25 小時。 您必須為剩餘的 0.25 小時支付預付型方案費率的費用。
3.  針對第 2 小時和第 3 小時，兩個執行個體各執行 1 小時。 保留執行個體的費用可涵蓋一個執行個體，而另一個執行個體則須依預付型方案的費率計費。
4.  針對第 4 個小時，執行個體 1 執行了 0.5 個小時，而執行個體 2 執行了 1 小時。 保留執行個體的費用可完全涵蓋執行個體 1，並涵蓋了執行個體 2 的 0.5 個小時。 您必須為剩餘的 0.5 小時支付預付型方案費率的費用。

若要在計費使用量報告中了解及檢視保留執行個體的應用，請參閱[了解保留執行個體使用量](https://go.microsoft.com/fwlink/?linkid=862757)。

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Windows 虛擬機器的保留執行個體折扣應用
當您執行 Windows 虛擬機器執行個體時，系統會套用保留執行個體以涵蓋基礎結構成本。 對於 Windows 虛擬機器，其虛擬機器基礎結構成本的保留執行個體應用與非 Windows 虛擬機器相同。 您必須為以每個 vCPU 為單位計算的 Windows 軟體個別支付費用。 請參閱[使用保留執行個體的 Windows 軟體成本](https://go.microsoft.com/fwlink/?linkid=862756)。 您可以利用 [適用於 Windows Server 的 Azure Hybrid Benefit] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)) 來涵蓋 Windows 授權成本。

## <a name="next-steps"></a>後續步驟
若要深入了解保留執行個體，請參閱下列文章：

- [使用 Azure 保留執行個體以節省虛擬機器的成本](billing-save-compute-costs-reservations.md)
- [預付具有保留執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理保留執行個體](billing-manage-reserved-vm-instance.md)
- [了解保留執行個體折扣如何套用](billing-understand-vm-reservation-charges.md)
- [了解預付型方案的保留執行個體使用量](billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊之保留執行個體的使用方式](billing-understand-reserved-instance-usage-ea.md)
- [了解適用於 CSP 訂用帳戶的保留執行個體使用方式](https://docs.microsoft.com/partner-center/azure-reservations) \(英文\)
- [Windows 軟體的成本不包括在保留的執行個體內](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果您仍有其他問題，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
