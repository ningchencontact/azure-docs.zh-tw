---
title: 取用 Azure Stack 中的監視資料 | Microsoft Docs
description: 了解可用來取用 Azure Stack 監視資料的選項。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2018
ms.author: mabrigg
ms.openlocfilehash: b6196ec434d00a6fbc6714095fa4182ede98ce91
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2018
ms.locfileid: "45633430"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>如何取用 Azure Stack 中的監視資料

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以使用 Azure 監視器管線在單一位置尋找監視資料，情形就和全域 Azure 中的 Azure 監視器一樣。 但並非所有可在全域 Azure 中找到的監視資料都可在 Azure Stack 中取得。 在本文中，您可以找到各種可行方式的摘要說明，以了解如何透過程式設計方式，從該服務擷取出監視資料。
 
## <a name="options-for-data-consumption"></a>資料取用的選項

| 資料類型 | 類別 | 支援的服務 | 存取方法 |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure 監視器平台層級計量 | 度量 | [Azure Stack 上 Azure 監視器的受支援計量](azure-stack-metrics-supported.md) | REST API |
| 計算客體 OS 計量 (例如效能計數) | 度量 | Windows 與 Linux 虛擬機器 | 儲存體資料表或 Blob：<br>Windows 或 Linux Azure 診斷 <br>事件中樞：<br>Microsoft Azure 診斷 |
| 儲存體度量 | 度量 | Azure 儲存體 | 儲存體資料表：<br>儲存體分析 |
| 活動記錄檔 | 活動 | 所有 Azure 服務 | REST API：<br>Azure 監視器事件 API |
| 計算客體 OS 記錄 (例如 IIS、ETW、syslog) | 活動 | Windows 與 Linux 虛擬機器 | 儲存體資料表或 Blob：<br>Windows 或 Linux Azure 診斷 <br>事件中樞：<br>Microsoft Azure 診斷 |
| 儲存體記錄 | 活動 | Azure 儲存體 | 儲存體資料表：<br>儲存體分析 |

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Stack 上的 Azure 監視器](azure-stack-metrics-azure-data.md)。
