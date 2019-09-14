---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968556"
---
**輸出資料傳輸**：[輸出資料傳輸](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

**交易**：將依據您在標準受控磁碟上執行的交易數向您收費。 針對標準 Ssd，小於或等於 256 KiB 輸送量的每個 i/o 作業都會被視為單一 i/o 作業。 大於 256 KiB 輸送量的 i/o 作業會視為大小為 256 KiB 的多個 i/o。 針對標準 Hdd，不論 i/o 大小為何，每個 IO 作業都會被視為單一交易。

如需受控磁碟定價的詳細資訊，包括交易成本，請參閱[受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra 磁片 VM 保留費用

Azure Vm 具有指示是否與 ultra 磁片相容的功能。 與 Ultra 磁碟相容的 VM 會在計算 VM 執行個體與區塊儲存體的縮放單位之間配置專用頻寬容量，以最佳化效能並減少延遲。 若在 VM 上新增此功能，只有在未將 Ultra 磁碟連結至該 VM 的情況下於 VM 上啟用 Ultra 磁碟功能時，才會產生保留費用。 當 Ultra 磁碟連結至與 Ultra 磁碟相容的 VM 時，就不會收取此費用。 此費用會根據在 VM 上佈建的 vCPU 計費。 

> [!Note]
> 對於[受條件約束的核心 VM 大小](../articles/virtual-machines/linux/constrained-vcpu.md)，保留費用是根據實際的個 vcpu 數目，而不是受限制的核心數。 針對 Standard_E32-8s_v3，保留費用將以32核心為基礎。 

如需 ultra 磁片定價詳細資料，請參閱[Azure 磁片定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)。
