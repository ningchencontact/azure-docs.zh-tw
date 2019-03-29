---
title: 管理程式的存取權檢閱-Azure Active Directory 的控制項 |Microsoft Docs
description: 了解如何收集和組織為控制項的 Azure Active Directory 存取權檢閱您組織中建立其他程式，讓每個治理、 風險管理和合規性方案。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c0f1c041bfed1b041a9926efd869d167c6f1e9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577258"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>管理程式和 Azure ad 的控制存取權檢閱

Azure Active Directory (Azure AD) 包含群組成員和應用程式存取權的存取權檢閱。 這些控制項的範例可確實監督誰可以使用您組織的群組成員資格和應用程式。 組織可使用這些控制項有效地處理其治理、風險管理和合規性需求。

## <a name="create-and-manage-programs-and-their-controls"></a>建立和管理程式及其控制項
您可以將追蹤和收集不同用途的存取權檢閱簡化，方法是將它們組織到程式中。 每個存取權檢閱可連結至一個程式。 然後當您為稽核員準備報告時，您可以著重於特定方案範圍內的存取權檢閱。  程式和存取權檢閱結果會顯示給全域管理員、 使用者管理員、 安全性系統管理員或安全性讀取者角色中的使用者。

若要查看程式清單，請移至[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，並選取 [程式集]。

一律會出現一個「預設程式」。 如果您是全域管理員或使用者系統管理員角色中，您可以建立其他程式。 比方說，您可以針對每個合規性方案或業務目標建立一個程式。

如果您不再需要某個程式，且該程式未連結任何控制項，就可以將程式刪除。

## <a name="next-steps"></a>後續步驟

- [建立群組或應用程式的存取權檢閱](create-access-review.md)
- [擷取群組或應用程式的存取權檢閱的結果](retrieve-access-review.md)
