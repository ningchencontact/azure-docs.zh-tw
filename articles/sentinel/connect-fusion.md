---
title: 啟用 Azure Sentinel 預覽版中的融合 |Microsoft Docs
description: 了解如何啟用在 Azure Sentinel 融合。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55d569d4a993a725137d7bfab37c113147fe81ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714908"
---
# <a name="enable-fusion"></a>啟用融合

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在 Azure Sentinel 的機器學習服務是從一開始的內建的。 我們念有設計旨在讓安全性分析師、 安全性資料科學家和工程師具生產力的 ML 創新系統。 一個這類創新是 Azure Sentinel Fusion 建置特別以減少警示疲勞。

融合數百萬個較低精確度異常活動時會從不同的產品，例如 Azure AD Identity Protection 和 Microsoft Cloud App Security，將它們結合成合理數量的相互關聯使用圖形提供機器學習服務演算法有趣的安全性案例。

## <a name="enable-fusion"></a>啟用融合

1. 在 Azure 入口網站中，選取圖示以開啟 Cloud Shell。
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  在 **歡迎使用 Cloud Shell**底下，會開啟的視窗選取 PowerShell。

3.  選擇您部署 Azure Sentinel，訂用帳戶及**建立儲存體**。

4. 之後您會進行驗證和建置您的 Azure 磁碟機，在命令提示字元中執行下列命令：

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>停用 fusion

請遵循與上述相同的程序，然後執行下列命令：

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>檢視融合的狀態

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>後續步驟

本文件中，您已了解如何啟用在 Azure Sentinel 融合。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

