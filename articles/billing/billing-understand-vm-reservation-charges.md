---
title: 了解 Azure 保留的 VM 執行個體折扣 | Microsoft Docs
description: 了解如何將 Azure 保留虛擬機器執行個體的折扣，套用至執行中的虛擬機器。
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
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370064"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>如何將 Azure 的保留折扣套用至虛擬機器

購買 Azure 保留的虛擬機器執行個體之後，保留折扣會自動套用至符合保留之屬性和數量的虛擬機器。 保留會涵蓋虛擬機器的計算成本。

保留折扣適用於您從 Azure Marketplace 購買的基底 Vm。

如需 SQL Database 保留容量，請參閱[了解 Azure 保留的執行個體折扣](billing-understand-reservation-charges.md)。

下表說明您購買保留的 VM 執行個體後的虛擬機器成本。 在所有情況下，您都必須依標準費率支付儲存體和網路功能的費用。

| 虛擬機器類型  | 保留的 VM 執行個體的費用 |
|-----------------------|--------------------------------------------|
|沒有包含其他軟體的 Linux 虛擬機器 | 保留可涵蓋您的 VM 基礎結構成本。|
|包含軟體 (例如 Red Hat) 費用的 Linux 虛擬機器 | 保留可涵蓋基礎結構成本。 您必須支付其他軟體的費用。|
|沒有包含其他軟體的 Windows 虛擬機器 |保留可涵蓋基礎結構成本。 您必須支付 Windows 軟體的費用。|
|包含其他軟體 (例如 SQL server) 的 Windows 虛擬機器 | 保留可涵蓋基礎結構成本。 您必須支付 Windows 軟體和其他軟體的費用。|
|具有 [Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) 的 Windows 虛擬機器 | 保留可涵蓋基礎結構成本。 Azure Hybrid Benefit 所涵蓋的 Windows 軟體成本。 任何額外的軟體都是分開收費。|

## <a name="how-reservation-discount-is-applied"></a>保留折扣如何套用

保留折扣會 「*使用-it-或-遺失-it*"。 因此，如果您沒有任何相符的資源，然後您會遺失保留數量的那一小時。 您無法執行轉送未使用保留的時數。

當您關閉資源時，保留折扣會自動套用至指定之範圍中的另一個相符的資源。 如果沒有相符的資源位於指定的範圍，則保留的時間為週間*遺失*。

## <a name="reservation-discount-for-non-windows-vms"></a>保留折扣的非 Windows Vm

 Azure 保留折扣會套用到正在執行的 VM 執行個體 (以小時計算)。 您購買的保留會與執行中虛擬機器發出的使用量進行比對，以便套用保留折扣。 對於沒有執行整個小時的 VM，保留會由其他不是使用保留的 VM (包括目前執行的 VM) 來填滿。 在一個小時結束時，將鎖定該小時內的 VM 保留應用。 如果 VM 執行時間未滿一小時，或該小時內的並行 VM 未填滿保留的一小時，即表示該小時的保留使用率過低。 下圖說明可計費虛擬機器使用量的保留應用。 圖例是以一個購買的保留和兩個對應的虛擬機器執行個體為基礎。

![一個套用的保留執和兩個對應的 VM 執行個體的螢幕擷取畫面](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. 高於保留線的任何使用量取得規則的隨用隨付費率來收費。 您不用支付低於保留線的使用費用，因為此費用已涵蓋於所購買的保留內。
2. 在第一個 1 小時內，執行個體 1 執行了 0.75 個小時，而執行個體 2 執行了 0.5 小時。 第一個 1 小時的使用量總計為 1.25 小時。 您必須為剩餘的 0.25 小時支付隨用隨付費率的費用。
3. 針對第 2 小時和第 3 小時，兩個執行個體各執行 1 小時。 一個執行個體涵蓋在保留內，而另一個執行個體須依隨用隨付的費率計費。
4. 針對第 4 個小時，執行個體 1 執行了 0.5 個小時，而執行個體 2 執行了 1 小時。 保留可完全涵蓋執行個體 1，並涵蓋了執行個體 2 的 0.5 個小時。 您必須為剩餘的 0.5 小時支付隨用隨付費率的費用。

若要在計費使用量報告中了解及檢視 Azure 保留的應用，請參閱[了解保留使用量](billing-understand-reserved-instance-usage-ea.md)。

## <a name="reservation-discount-for-windows-vms"></a>保留折扣適用於 Windows Vm

當您執行 Windows VM 執行個體時，系統會套用保留以涵蓋基礎結構成本。 對於 Windows 虛擬機器，其虛擬機器基礎結構成本的保留應用與非 Windows 虛擬機器相同。 您必須為以每個 vCPU 為單位計算的 Windows 軟體個別支付費用。 請參閱 [使用保留的 Windows 軟體成本](billing-reserved-Instance-windows-software-costs.md)。 您可以使用[適用於 Windows Server 的 Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) 來涵蓋 Windows 授權成本。

## <a name="discount-can-apply-to-different-sizes"></a>折扣可以套用至不同的大小

當您購買保留的 VM 執行個體時，如果選取 [最佳化對象]  ：[執行個體大小彈性]  ，折扣涵蓋範圍就會依據您選取的 VM 大小。 此保留可適用於相同大小系列群組中的虛擬機器 (VM) 大小。 如需詳細資訊，請參閱[利用保留的 VM 執行個體獲得虛擬機器大小彈性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。

## <a name="discount-applies-to-matching-servicetype-only"></a>折扣適用於只比對 ServiceType

保留折扣僅適用於 VM 使用量所在`ServiceType`中的值`AdditionalInfo`符合購買保留項目。 保留折扣應用程式會忽略針對 Vm 所使用之計量，並只會評估`ServiceType`。 了解哪一種服務，輸入您購買的 VM。 您可以交換非進階儲存體 VM 保留的 premium 儲存體的保留區，或以相反的方式。

## <a name="classic-vms-and-cloud-services"></a>傳統的 Vm 和雲端服務

保留的 VM 執行個體自動套用至這兩個傳統 Vm，並啟用執行個體大小彈性時，雲端服務。 雲端服務的保留折扣僅適用於計算成本。 當保留折扣套用至雲端服務時，使用量費用會分成計算費用 （Linux 計量），而且雲端服務 （雲端服務管理計量） 的費用。 如需詳細資訊，請參閱 <<c0> [ 保留折扣套用至雲端服務的方式](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost)。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [Azure 保留項目有哪些？](billing-save-compute-costs-reservations.md)
- [預付具有 Azure 保留 VM 執行個體的虛擬機器](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [以 Azure SQL Database 保留容量預先支付 SQL 資料庫計算資源的費用](../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
- [了解隨用隨付方案訂用帳戶的保留項目使用量](billing-understand-reserved-instance-usage.md)
- [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
- [了解 CSP 訂用帳戶的保留使用量](/partner-center/azure-reservations)
- [Windows 軟體的成本不包括在保留內](billing-reserved-instance-windows-software-costs.md)
