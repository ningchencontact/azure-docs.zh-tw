---
title: Symantec ICDX 資料連接至 Azure 的 Sentinel 預覽 |Microsoft Docs
description: 了解如何將 Symantec ICDX 資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502079"
---
# <a name="connect-your-symantec-icdx-appliance"></a>連接您的 Symantec ICDX 應用裝置 

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Symantec ICDX 連接器可讓您輕鬆地連接所有您 Symantec 安全性解決方案記錄您 Azure Sentinel，來檢視儀表板、 建立自訂警示，以及改善調查。 這可讓您更多深入您的組織網路，並改善您的安全性作業功能。 Symantec ICDX 與 Azure Sentinel 之間的整合會使用 REST API。


> [!NOTE]
> 資料會儲存在您在執行 Azure Sentinel 的工作區的地理位置。

## <a name="configure-and-connect-symantec-icdx"></a>設定及連接 Symantec ICDX 

Symantec ICDX 整合和記錄檔將直接匯出至 Azure 的 Sentinel。

1. 開啟 ICDX 管理主控台。
2. 在左側的導覽功能表中，選取 **組態**，然後 **轉寄站** 索引標籤。
3. 在 Microsoft Azure Log Analytics 資料列中，按一下**更多**，後面接著**編輯**。 
4. 在 [ **Microsoft Azure Log Analytics 轉寄站**] 視窗中，進行下列設定：
    - 將自訂記錄檔名稱保留為預設值，SymantecICDX。
    - 複製的工作區識別碼，並將它貼 **客戶識別碼**欄位。 複製**主索引鍵**並將它貼在 [共用金鑰] 欄位。 您可以從 Azure Sentinel 入口網站複製這些值，方法是選取**資料連接器**，然後**Symantec ICDX**。
6. 若要使用 Log Analytics 中的 Symantec ICDX 事件相關的結構描述，搜尋**SymantecICDX_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要多達 20 分鐘，直到您的記錄檔開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 Symantec ICDX。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

