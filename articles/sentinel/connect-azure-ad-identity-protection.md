---
title: 將 Azure AD Identity Protection 資料連線到 Azure Sentinel 預覽 |Microsoft Docs
description: 瞭解如何將 Azure AD Identity Protection 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 7478e5a5ec2260760bb6ddb1a90a66e3acdf2201
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129266"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>從 Azure AD Identity Protection 連接資料

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以將[Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)的記錄串流至 Azure Sentinel, 以將警示串流至 Azure Sentinel 以查看儀表板、建立自訂警示, 以及改善調查。 Azure Active Directory Identity Protection 提供有風險的使用者、風險偵測和弱點的匯總視圖, 並能夠立即補救風險, 以及設定原則來自動補救未來的事件。 這項服務是以 Microsoft 的經驗為基礎來保護取用者身分識別, 並從一天超過13000000000個登入的信號獲得極高的精確度。 


## <a name="prerequisites"></a>必要條件

- 您必須擁有[Azure Active Directory Premium P1 或 P2 授權](https://azure.microsoft.com/pricing/details/active-directory/)
- 具有全域管理員或安全性系統管理員許可權的使用者


## <a name="connect-to-azure-ad-identity-protection"></a>連接到 Azure AD Identity Protection

如果您已經有 Azure AD Identity Protection, 請確定已[在您的網路上啟用該功能](../active-directory/identity-protection/enable.md)。
如果 Azure AD Identity Protection 已部署並取得資料, 則可以輕鬆地將警示資料串流至 Azure Sentinel。


1. 在 Azure Sentinel 中, 選取 [**資料連線器**], 然後按一下 [ **Azure AD Identity Protection** ] 磚。

2. 按一下 **[連接]** 開始將 Azure AD Identity Protection 事件串流至 Azure Sentinel。


6. 若要在 Log Analytics 中針對 Azure AD Identity Protection 警示使用相關的架構, 請搜尋**IdentityProtectionLogs_CL**。

## <a name="next-steps"></a>後續步驟
在本檔中, 您已瞭解如何將 Azure AD Identity Protection 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
