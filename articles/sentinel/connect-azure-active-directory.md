---
title: 將 Azure AD 資料連接至 Azure 的 Sentinel Preview |Microsoft Docs
description: 了解如何將 Azure Active Directory 資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: f29ae9a8fcaecfc345efae02084f31d133e67b5d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786858"
---
# <a name="connect-data-from-azure-active-directory"></a>從 Azure Active Directory 連線資料

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 的 Sentinel 可讓您要從中收集資料[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)並將它串流到 Azure 的 Sentinel。 您可以選擇資料流[單一登入](../active-directory/reports-monitoring/concept-sign-ins.md)並[稽核記錄檔](../active-directory/reports-monitoring/concept-audit-logs.md)。

## <a name="prerequisites"></a>必要條件

- 如果您想要匯出從 Active Directory 的登入資料，您必須將 Azure AD P1 或 P2 授權。

- 具有全域管理員或安全性系統管理員權限在您想要從記錄檔串流的租用戶上的使用者。


## <a name="connect-to-azure-ad"></a>連接至 Azure AD

1. 在 Azure Sentinel，選取**資料連接器**，然後按一下**Azure Active Directory**圖格。

2. 記錄檔，您想要串流處理至 Azure 的 Sentinel，旁邊按一下  **Connect**。

6. 若要使用 Log Analytics 中的 Azure AD 警示相關的結構描述，搜尋**SigninLogs**並**AuditLogs**。




## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 Azure AD。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
