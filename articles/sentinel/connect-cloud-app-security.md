---
title: 將 Cloud App Security 資料連線到 Azure Sentinel 預覽 |Microsoft Docs
description: 瞭解如何將 Cloud App Security 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 92de4120d73af70c5de013488344a15ba46513c7
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881078"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>從 Microsoft Cloud App Security 連接資料 

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您只要按一下, 就可以將[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)的記錄串流至 Azure Sentinel。 此連接可讓您將 Cloud App Security 的警示串流至 Azure Sentinel。 

## <a name="prerequisites"></a>先決條件

- 具有全域管理員或安全性系統管理員許可權的使用者

## <a name="connect-to-cloud-app-security"></a>連接到 Cloud App Security

如果您已經有 Cloud App Security, 請確定已[在您的網路上啟用該功能](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果 Cloud App Security 已部署並內嵌您的資料, 則可以輕鬆地將警示資料串流至 Azure Sentinel。


1. 在 Azure Sentinel 中, 選取 [**資料連線器**], 然後按一下 [ **Cloud App Security** ] 磚。

2. 按一下 **[連接]** 。

3. 若要在 Log Analytics 中針對 Cloud App Security 警示使用相關的架構, 請搜尋**SecurityAlert**。


## <a name="next-steps"></a>後續步驟
在本檔中, 您已瞭解如何將 Microsoft Cloud App Security 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
