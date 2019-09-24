---
title: 將 Azure 活動資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Azure 活動資料連線至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 807a5ede3feee115b1a8dc51fe14966731fc7784
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240780"
---
# <a name="connect-data-from-azure-activity-log"></a>從 Azure 活動記錄連接資料



只要按一下，您就可以將記錄從[Azure 活動記錄](../azure-monitor/platform/activity-logs-overview.md)串流至 Azure Sentinel。 活動記錄是訂用客戶紀錄，可讓您深入瞭解 Azure 中發生的訂用帳戶層級事件。 所涵蓋的資料範圍從 Azure Resource Manager 作業資料到服務健康情況事件的更新。 使用活動記錄，您可以判斷訂用帳戶中資源上任何寫入作業（PUT、POST、DELETE）的「內容、物件和時間」。 您也可以了解作業的狀態和其他相關屬性。 活動記錄不包含讀取（GET）作業，或是使用傳統/"RDFE" 模型之資源的作業。 


## <a name="prerequisites"></a>必要條件

- 具有全域管理員或安全性系統管理員許可權的使用者


## <a name="connect-to-azure-activity-log"></a>連接到 Azure 活動記錄

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後按一下 [ **Azure 活動記錄**] 圖格。

2. 在 [Azure 活動記錄檔] 窗格中，選取您想要串流至 Azure Sentinel 的訂用帳戶。 

3. 按一下 **[連接]** 。

4. 若要在 Log Analytics 中針對 Azure 活動警示使用相關的架構，請搜尋**AzureActivity**。


 

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure 活動記錄連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
