---
title: 將 Office 365 資料連接至 Azure 的 Sentinel Preview |Microsoft Docs
description: 了解如何將 Office 365 資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1d6a467307e4816ffbb45f23bac55b8023267352
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611244"
---
# <a name="connect-data-from-office-365-logs"></a>連接 Office 365 記錄資料

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以串流處理從稽核記錄[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide)到 Azure Sentinel 只要按一下。 您可以串流來自多個租用戶中 Azure Sentinel 的單一工作區的稽核記錄檔。 Office 365 活動記錄連接器提供進行中的使用者活動的深入解析。 您會從 Office 365 各種使用者、 系統管理員、 系統和原則動作和事件的相關資訊。 藉由連接到 Azure Sentinel 的 Office 365 記錄檔中，您可以使用這項資料來檢視儀表板、 建立自訂警示，並改善您的調查程序。


## <a name="prerequisites"></a>必要條件

- 您必須是全域管理員或安全性系統管理員在您的租用戶
- 您在電腦上，您登入 Azure Sentinel 來建立連線，請確定已對 web 流量開啟連接埠 4433。

## <a name="connect-to-office-365"></a>連接至 Office 365

1. 在 Azure Sentinel，選取**資料連接器**，然後按一下**Office 365**圖格。

2. 如果您沒有已啟用，底下**連接**使用**啟用**按鈕來啟用 Office 365 解決方案。 如果已啟用，也會在已啟用的 [連接] 畫面中識別它。
1. Office 365 可讓您將資料串流處理來自 Azure Sentinel 的多重租用戶中。 針對您想要連接到每個租用戶，新增 租用戶**將租用戶連接到 Azure 的 Sentinel**。 
1. Active Directory 畫面隨即開啟。 系統會提示您使用您想要連接到 Azure 的 Sentinel，並對 Azure Sentinel 閱讀其記錄檔提供權限的每個租用戶上全域系統管理員使用者進行驗證。 
5. 在 Stream 的 Office 365 活動記錄檔中，按一下**選取**來選擇您想要串流處理至 Azure 的 Sentinel 哪一個記錄類型。 目前，Azure Sentinel 支援的 Exchange 和 SharePoint。

4. 按一下 **套用變更**。

3. 若要使用 Log Analytics 中的 Office 365 記錄相關的結構描述，搜尋**OfficeActivity**。


## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何將 Office 365 連線至 Azure 的 Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

