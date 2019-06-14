---
title: Azure AD Identity Protection 資料連接至 Azure 的 Sentinel Preview |Microsoft Docs
description: 了解如何將 Azure AD Identity Protection 資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 10dc31e21f20618450de6d99b3fce40d63272d31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204383"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>從 Azure AD Identity Protection 連線資料

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以從串流記錄[Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)到 Azure Sentinel 串流到 Azure Sentinel 檢視儀表板、 建立自訂警示，並改善調查的警示。 Azure Active Directory Identity Protection 提供的合併的檢視具風險使用者、 風險事件及弱點，並能夠立即修復風險，以及設定原則以自動修復未來的事件。 此服務建基於 Microsoft 保護消費者身分識別的經驗，並獲得極大的訊號達到 13 億個登入一天。 


## <a name="prerequisites"></a>必要條件

- 您必須擁有[Azure Active Directory Premium P1 或 P2 的授權](https://azure.microsoft.com/pricing/details/active-directory/)
- 以全域系統管理員或安全性系統管理員權限的使用者


## <a name="connect-to-azure-ad-identity-protection"></a>連接到 Azure AD Identity Protection

如果您已經有 Azure AD Identity Protection，請確定它是[在您網路上啟用](../active-directory/identity-protection/enable.md)。
如果在部署 Azure AD Identity Protection，並取得資料，請參閱警示的資料可以輕鬆地串流到 Azure 的 Sentinel。


1. 在 Azure Sentinel，選取**資料連接器**，然後按一下**Azure AD Identity Protection**圖格。

2. 按一下  **Connect**開始串流處理至 Azure 的 Sentinel 的 Azure AD Identity Protection 事件。


6. 若要使用 Log Analytics 中的 Azure AD Identity Protection 警示相關的結構描述，搜尋**IdentityProtectionLogs_CL**。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 Azure AD Identity Protection。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
