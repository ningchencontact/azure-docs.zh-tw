---
title: 收集 Azure Sentinel 預覽版中的 Windows 防火牆資料 |Microsoft Docs
description: 了解如何收集 Azure Sentinel 的 Windows 防火牆資料。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2356a7e5426037ffe9fc8b304ac113f4a3fe2a17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103344"
---
# <a name="connect-windows-firewall"></a>連線 Windows 防火牆

> [!IMPORTANT]
> Azure 的 Sentinel 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果連線到您 Azure Sentinel 的工作區的 Windows 防火牆連接器可讓您輕鬆地連接您的 Windows 防火牆記錄。 此連線可讓您檢視儀表板、 建立自訂警示，以及改善的調查。 這可讓您更多深入您的組織網路，並改善您的安全性作業功能。  


> [!NOTE]
> 
> - 資料會儲存在您在執行 Azure Sentinel 的工作區的地理位置。

## <a name="enable-the-connector"></a>啟用連接器 

1. 在 Azure Sentinel 入口網站中，選取**資料收集**，然後按一下**Windows 防火牆**圖格。 
1. 選取您要串流處理的資料類型。
1. 按一下 [Install] 。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要多達 20 分鐘，直到您的記錄檔開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接到 Azure Sentinel 的 Windows 防火牆。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

