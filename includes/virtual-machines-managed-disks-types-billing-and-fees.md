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
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443311"
---
**輸出資料傳輸**：[輸出資料傳輸](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

**交易**：將依據您在標準受控磁碟上執行的交易數向您收費。 在標準 HHD 上，Azure 每 100,000 筆交易會收取 $0.0036。 交易一概包括對儲存體進行的讀取和寫入作業。

標準 SSD 使用 256 KB 的 IO 單位大小。 如果要傳送的資料少於 256 KB，會視為 1 個 I/O 單位。 較大的 I/O 大小則會視為大小是 256 KB 的多個 I/O。 例如，1100 KB 的 I/O 會視為五個 I/O 單位。

進階受控磁碟沒有交易成本。

如需受控磁碟價格的詳細資訊，請參閱[受控磁碟價格](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM 保留費用

Azure VM 可以指出它們是否與 Ultra SSD 相容。 與 Ultra 磁碟相容的 VM 會在計算 VM 執行個體與區塊儲存體的縮放單位之間配置專用頻寬容量，以最佳化效能並減少延遲。 若在 VM 上新增此功能，只有在未將 Ultra 磁碟連結至該 VM 的情況下於 VM 上啟用 Ultra 磁碟功能時，才會產生保留費用。 當 Ultra 磁碟連結至與 Ultra 磁碟相容的 VM 時，就不會收取此費用。 此費用會根據在 VM 上佈建的 vCPU 計費。

請參閱 [Azure 磁碟定價頁面](https://azure.microsoft.com/pricing/details/managed-disks/)以取得 Ultra 磁碟定價詳細資料。