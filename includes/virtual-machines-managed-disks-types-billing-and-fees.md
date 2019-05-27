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
ms.openlocfilehash: 268467796e67caf2d20fedb44d83fd455a09b83e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147878"
---
**輸出資料傳輸**：[輸出資料傳輸](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

**交易**：將依據您在標準受控磁碟上執行的交易數向您收費。 標準的 Ssd，每個 I/O 作業小於或等於 256 KiB 的輸送量會被視為單一 I/O 作業。 大於 256 KiB 的輸送量的 I/O 作業會被視為多個 I/o 大小 256 KiB。 針對標準的 Hdd，每個的 IO 作業都會被視為單一交易，不論的 I/O 大小。

如需受控磁碟，包括交易成本的定價詳細資訊，請參閱[受控磁碟價格](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM 保留費用

Azure VM 可以指出它們是否與 Ultra SSD 相容。 與 Ultra 磁碟相容的 VM 會在計算 VM 執行個體與區塊儲存體的縮放單位之間配置專用頻寬容量，以最佳化效能並減少延遲。 若在 VM 上新增此功能，只有在未將 Ultra 磁碟連結至該 VM 的情況下於 VM 上啟用 Ultra 磁碟功能時，才會產生保留費用。 當 Ultra 磁碟連結至與 Ultra 磁碟相容的 VM 時，就不會收取此費用。 此費用會根據在 VM 上佈建的 vCPU 計費。

請參閱 [Azure 磁碟定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)以取得 Ultra 磁碟定價詳細資料。