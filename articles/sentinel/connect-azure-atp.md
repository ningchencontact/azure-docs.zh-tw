---
title: 將 Azure ATP 資料連線到 Azure Sentinel 預覽 |Microsoft Docs
description: 瞭解如何將 Azure ATP 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6f41a5704c783ba8aeab7bc2e82ef731c6a257ac
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599148"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>從 Azure 進階威脅防護連接資料 (ATP)

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


您只要按一下, 就可以將[Azure 進階威脅防護](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp)的記錄串流至 Azure Sentinel。

## <a name="prerequisites"></a>先決條件

- 具有全域管理員或安全性系統管理員許可權的使用者
- 您必須是 Azure ATP 的預覽客戶

## <a name="connect-to-azure-atp"></a>連接到 Azure ATP

請確定已[在您的網路上啟用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Azure ATP preview 版本。
如果 Azure ATP 已部署並內嵌您的資料, 則可以輕鬆地將可疑的警示串流至 Azure Sentinel。 最多可能需要24小時的時間, 警示才會開始串流至 Azure Sentinel。


1. 若要將 Azure ATP 連接到 Azure Sentinel, 您必須先啟用 Azure ATP 與 Microsoft Cloud App Security 之間的整合。 如需如何執行此動作的詳細資訊, 請參閱[Azure 進階威脅防護整合](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure Sentinel 中, 選取 [**資料連線器**], 然後按一下 [ **Azure ATP** ] 磚。

2. 按一下 **[連接]** 。

6. 若要在 Log Analytics 中針對 Azure ATP 警示使用相關的架構, 請搜尋**SecurityAlert**。

## <a name="next-steps"></a>後續步驟
在本檔中, 您已瞭解如何將 Azure 進階威脅防護連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

