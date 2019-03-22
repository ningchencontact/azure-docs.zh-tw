---
title: 收集 Azure Sentinel 預覽版中的 Windows 安全性事件資料 |Microsoft Docs
description: 了解如何收集 Azure Sentinel 中的 Windows 安全性事件資料。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 3c79747bf33e1769af5f8d3589904ba15105f216
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087596"
---
# <a name="connect-windows-security-events"></a>連線 Windows 安全性事件 

> [!IMPORTANT]
> Azure 的 Sentinel 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以串流處理來自連接到您 Azure Sentinel 的工作區之 Windows 伺服器的所有安全性事件。 此連線可讓您檢視儀表板、 建立自訂警示，以及改善的調查。 這可讓您更多深入您的組織網路，並改善您的安全性作業功能。  您可以選取所要串流處理事件：

- **所有事件**-所有的 Windows 安全性及 AppLocker 事件。
- **常見**-一組標準的稽核事件。
- **最小**位較少的可能指出潛在的威脅的事件。 藉由啟用此選項，您無法擁有完整的稽核線索。
- **無**-沒有安全性或 AppLocker 事件。

> [!NOTE]
> 
> - 資料會儲存在您在執行 Azure Sentinel 的工作區的地理位置。

## <a name="set-up-the-windows-security-events-connector"></a>設定 Windows 安全性事件 connector

若要完全與 Azure Sentinel 整合您的 Windows 安全性事件：

1. 在 Azure Sentinel 入口網站中，選取**資料收集**，然後按一下**Windows 安全性事件**圖格。 
1. 選取您要串流處理的資料類型。
1. 按一下 [更新] 。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要大約 20 分鐘，直到您的記錄檔開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何將 Windows 安全性事件連線至 Azure 的 Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

