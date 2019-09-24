---
title: 將 Azure ATP 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Azure ATP 資料連線到 Azure Sentinel。
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
ms.openlocfilehash: ebb727055296ba7886a9307ada113ab5a6e0c9e0
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240178"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp---preview"></a>從 Azure 進階威脅防護連接資料（ATP）-預覽




您只要按一下，就可以將[Azure 進階威脅防護](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp)的記錄串流至 Azure Sentinel。

## <a name="prerequisites"></a>必要條件

- 具有全域管理員或安全性系統管理員許可權的使用者
- 您必須是 Azure ATP 的預覽客戶

## <a name="connect-to-azure-atp"></a>連接到 Azure ATP

請確定已[在您的網路上啟用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Azure ATP preview 版本。
如果 Azure ATP 已部署並內嵌您的資料，則可以輕鬆地將可疑的警示串流至 Azure Sentinel。 最多可能需要24小時的時間，警示才會開始串流至 Azure Sentinel。


1. 若要將 Azure ATP 連接到 Azure Sentinel，您必須先啟用 Azure ATP 與 Microsoft Cloud App Security 之間的整合。 如需如何執行此動作的詳細資訊，請參閱[Azure 進階威脅防護整合](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後按一下 [ **Azure ATP** ] 磚。

1. 您可以選取是否要讓警示 Azure ATP 自動在 Azure Sentinel 中自動產生事件。 在 [**建立事件**] 底下，選取 [**啟用**] 以啟用預設分析規則，以自動從已連線的安全性服務中產生的警示建立事件。 接著，您可以在 [**分析**] 和 [作用中**規則**] 底下編輯此規則。

1. 按一下 **[連接]** 。

1. 若要在 Log Analytics 中針對 Azure ATP 警示使用相關的架構，請搜尋**SecurityAlert**。

> [!NOTE]
> 如果警示大於 30 KB，Azure Sentinel 會停止在警示中顯示 [實體] 欄位。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure 進階威脅防護連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

