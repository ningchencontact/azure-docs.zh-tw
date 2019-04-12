---
title: Barracuda 資料連接至 Azure 的 Sentinel 預覽 |Microsoft Docs
description: 了解如何將 Barracuda 資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d8e92fd3918230b48449926dcbb7528d919fd96f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492833"
---
# <a name="connect-your-barracuda-appliance"></a>連接您的 Barracuda 應用裝置 

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Barracuda Web 應用程式防火牆 (WAF) 連接器可讓您輕鬆地連接您的 Barracuda 記錄您 Azure Sentinel，來檢視儀表板、 建立自訂警示，以及改善調查。 這可讓您更多深入您的組織網路，並改善您的安全性作業功能。 Azure 的 Sentinel 利用之間的原生整合**Barracuda**和 Microsoft Azure OMS 提供無縫整合。 


> [!NOTE]
> 資料會儲存在您在執行 Azure Sentinel 的工作區的地理位置。

## <a name="configure-and-connect-barracuda-waf"></a>設定及連接 Barracuda WAF
Barracuda Web 應用程式防火牆整合和記錄檔將直接匯出至 Azure Sentinel 透過 Azure OMS 的伺服器。
1. 移至[Barracuda WAF 設定流程](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)，並遵循指示來設定連線，使用下列參數：
    - **工作區識別碼**： 從 Azure Sentinel Barracuda 連接器頁面複製您的工作區識別碼的值。
    - **主索引鍵**： 從 Azure Sentinel Barracuda 連接器頁面複製您的主索引鍵的值。
2. 在 Azure Sentinel 入口網站中，移至您部署 Azure Sentinel 的工作區，然後選取省略符號 （...），結尾的資料列，然後選取**進階設定**。 
1. 選取 **資料**，然後**Syslog**。
1. 請確定您在 Barracuda 中設定的功能存在，並設定的嚴重性，然後按一下**儲存**。
6. 若要使用 Log Analytics 中的 Barracuda 事件相關的結構描述，搜尋**CommonSecurityLog**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要多達 20 分鐘，直到您的記錄檔開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 Barracuda 應用裝置。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

