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
ms.openlocfilehash: 6f6d373d6dd21f3fd04ca434678ec798146a0ffa
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512693"
---
**輸出資料傳輸**：[輸出資料傳輸](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

**交易**：將依據您在標準受控磁碟上執行的交易數向您收費。 針對標準 Ssd, 小於或等於 256 KiB 輸送量的每個 i/o 作業都會被視為單一 i/o 作業。 大於 256 KiB 輸送量的 i/o 作業會視為大小為 256 KiB 的多個 i/o。 針對標準 Hdd, 不論 i/o 大小為何, 每個 IO 作業都會被視為單一交易。

如需受控磁碟定價的詳細資訊, 包括交易成本, 請參閱[受控磁碟定價](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra 磁片 VM 保留費用

Azure Vm 具有指示是否與 ultra 磁片相容的功能。 與 Ultra 磁碟相容的 VM 會在計算 VM 執行個體與區塊儲存體的縮放單位之間配置專用頻寬容量，以最佳化效能並減少延遲。 若在 VM 上新增此功能，只有在未將 Ultra 磁碟連結至該 VM 的情況下於 VM 上啟用 Ultra 磁碟功能時，才會產生保留費用。 當 Ultra 磁碟連結至與 Ultra 磁碟相容的 VM 時，就不會收取此費用。 此費用會根據在 VM 上佈建的 vCPU 計費。

如需 ultra 磁片定價詳細資料, 請參閱[Azure 磁片定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)。