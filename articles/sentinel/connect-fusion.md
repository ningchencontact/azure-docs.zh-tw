---
title: 在 Azure Sentinel 預覽中啟用融合 |Microsoft Docs
description: 瞭解如何在 Azure Sentinel 中啟用融合。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780450"
---
# <a name="enable-fusion"></a>啟用融合

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 中的 Machine Learning 是從一開始就內建的。 我們已 thoughtfully 為系統設計 ML 創新, 其目標是要讓安全性分析師、安全性資料科學家和工程師保持生產力。 其中一項創新是 Azure Sentinel 融合功能, 專門為了減少警示疲勞而打造。

融合使用圖表支援的機器學習演算法, 從不同產品 (例如 Azure AD Identity Protection 和 Microsoft Cloud App Security) 中數百萬個較低精確度的異常活動相互關聯, 以將它們結合成可管理的數目有趣的安全性事件。

## <a name="enable-fusion"></a>啟用融合

1. 在 Azure 入口網站中, 選取圖示以開啟 Cloud Shell。
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  在下方開啟的 [**歡迎使用 Cloud Shell** ] 視窗中, 選取 [PowerShell]。

3.  選擇您用來部署 Azure Sentinel 的訂用帳戶, 並**建立儲存體**。

4. 在您通過驗證並建立您的 Azure 磁片磁碟機之後, 請在命令提示字元中執行下列命令:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>停用融合

遵循與上述相同的程式, 然後執行下列命令:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>查看融合的狀態

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>後續步驟

在本檔中, 您已瞭解如何在 Azure Sentinel 中啟用融合。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。

