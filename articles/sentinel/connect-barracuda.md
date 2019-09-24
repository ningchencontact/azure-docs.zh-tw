---
title: 將 Barracuda 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Barracuda 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: b3ca93d9e70456d25d5f78b2ca1fde8e4ea24f8d
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240211"
---
# <a name="connect-your-barracuda-appliance"></a>連接您的 Barracuda 應用裝置 



Barracuda Web 應用程式防火牆（WAF）連接器可讓您輕鬆地將 Barracuda 記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改善調查。 這可讓您深入瞭解組織的網路，並改善您的安全性作業功能。 Azure Sentinel 利用**Barracuda**和 Log Analytics 代理程式之間的原生整合來提供完美的整合。 


> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-barracuda-waf"></a>設定及連接 Barracuda WAF
Barracuda Web 應用程式防火牆可以透過 Log Analytics 代理程式，將記錄直接整合到 Azure Sentinel，並將其匯出。
1. 移至[BARRACUDA WAF 設定流程](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)，並遵循指示以使用下列參數來設定連線：
    - **工作區識別碼**：從 [Azure Sentinel Barracuda 連接器] 頁面複製工作區識別碼的值。
    - **主要金鑰**：從 Azure Sentinel Barracuda 連接器 頁面複製主要金鑰的值。
2. 在 Azure Sentinel 入口網站中，移至您已部署 Azure Sentinel 的工作區，然後選取資料列結尾處的省略號（...），然後選取 [ **Advanced settings**] （設定）。 
1. 依序選取 [**資料**] 和 [ **Syslog**]。
1. 請確定您在 Barracuda 中設定的設施存在，並設定嚴重性，然後按一下 [**儲存**]。
6. 若要在 Log Analytics 中針對 Barracuda 事件使用相關的架構，請搜尋**CommonSecurityLog**和**barracuda_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Barracuda 設備連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

