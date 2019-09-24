---
title: 將 Cloud App Security 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Cloud App Security 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6e71028855344d083dedf3493682e1e27685de48
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240114"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>從 Microsoft Cloud App Security 連接資料 



您只要按一下, 就可以將[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)的記錄串流至 Azure Sentinel。 此連接可讓您將 Cloud App Security 的警示串流至 Azure Sentinel。 

## <a name="prerequisites"></a>必要條件

- 具有全域管理員或安全性系統管理員許可權的使用者

## <a name="connect-to-cloud-app-security"></a>連接到 Cloud App Security

如果您已經有 Cloud App Security, 請確定已[在您的網路上啟用該功能](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果 Cloud App Security 已部署並內嵌您的資料, 則可以輕鬆地將警示資料串流至 Azure Sentinel。


1. 在 Azure Sentinel 中, 選取 [**資料連線器**], 然後按一下 [ **Cloud App Security** ] 磚。

1. 選取您想要串流至 Azure Sentinel 的記錄檔，您可以選擇 [**警示**]。 

1. 您可以選取是否要讓警示 Microsoft Cloud App Security 自動在 Azure Sentinel 中自動產生事件。 在 [**建立事件**] 底下，選取 [**啟用**] 以啟用預設分析規則，以自動從已連線的安全性服務中產生的警示建立事件。 接著，您可以在 [**分析**] 和 [作用中**規則**] 底下編輯此規則。

1. 按一下 **[連接]** 。

1. 若要在 Log Analytics 中針對 Cloud App Security 警示使用相關的架構, 請搜尋**SecurityAlert**。




## <a name="next-steps"></a>後續步驟
在本檔中, 您已瞭解如何將 Microsoft Cloud App Security 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
