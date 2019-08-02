---
title: 將 Office 365 資料連線到 Azure Sentinel 預覽 |Microsoft Docs
description: 瞭解如何將 Office 365 資料連線到 Azure Sentinel。
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
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 0013540bf0ca921b2f41260dea185f6aa32567d7
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679254"
---
# <a name="connect-data-from-office-365-logs"></a>從 Office 365 記錄連接資料

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您只要按一下, 就可以將來自[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide)的 audit 記錄串流至 Azure Sentinel。 您可以將多個租使用者的 audit 記錄串流至 Azure Sentinel 中的單一工作區。 Office 365 活動記錄連接器可讓您深入瞭解進行中的使用者活動。 您會從 Office 365 取得各種使用者、系統管理員、系統和原則動作和事件的相關資訊。 將 Office 365 記錄連接到 Azure Sentinel 您就可以使用此資料來觀看儀表板、建立自訂警示, 並改善您的調查流程。

> [!IMPORTANT]
> 如果您有 E3 授權, 在可以透過 Office 365 管理活動 API 存取資料之前, 必須先為您的 Office 365 組織啟用統一的審核記錄。 您可以藉由開啟 Office 365 audit 記錄來完成此動作。 如需指示, 請參閱[開啟或關閉 Office 365 審核記錄搜尋](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。 如需詳細資訊, 請參閱[Office 365 管理活動 API 參考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。

## <a name="prerequisites"></a>必要條件

- 您必須是租使用者的全域管理員或安全性系統管理員
- 在您的電腦上登入 Azure Sentinel 以建立連線, 請確定埠4433已開放給網路流量。

## <a name="connect-to-office-365"></a>連接至 Office 365

1. 在 Azure Sentinel 中, 選取 [**資料連線器**], 然後按一下 [ **Office 365** ] 磚。

2. 如果您尚未啟用它, 請在 [連線 ] 底下, 使用 [**啟用**] 按鈕來啟用 Office 365 解決方案。 如果已啟用, 則會在連線畫面中將其識別為已啟用。
1. Office 365 可讓您將多個租使用者的資料串流至 Azure Sentinel。 針對您想要連線的每個租使用者, 在 [將租使用者連線**到 Azure Sentinel]** 底下新增租使用者。 
1. [Active Directory] 畫面隨即開啟。 系統會提示您在每個您想要連線到 Azure Sentinel 的租使用者上, 使用全域管理員使用者進行驗證, 並提供 Azure Sentinel 讀取其記錄檔的許可權。 
5. 在 [Stream Office 365 活動記錄] 底下, 按一下 [**選取**] 以選擇您想要串流至 Azure Sentinel 的記錄類型。 目前, Azure Sentinel 支援 Exchange 和 SharePoint。

4. 按一下 [套用**變更**]。

3. 若要在 Log Analytics 中使用 Office 365 記錄的相關架構, 請搜尋**OfficeActivity**。


## <a name="next-steps"></a>後續步驟
在本檔中, 您已瞭解如何將 Office 365 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

