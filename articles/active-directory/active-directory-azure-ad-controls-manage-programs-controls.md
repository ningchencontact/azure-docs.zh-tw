---
title: 管理 Azure AD 存取權檢閱的程式和控制項 | Microsoft Docs
description: 您可以為組織中的每個治理、風險管理和合規性方案建立其他程式，以收集 Azure Active Directory 存取權檢閱並將其組織為控制項。
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.openlocfilehash: 06e91b4d52a70ea33f4317a6434c2bd49e5c3bd3
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37020969"
---
# <a name="manage-programs-and-their-controls"></a>管理程式及其控制項 

Azure Active Directory (Azure AD) 包含群組成員和應用程式存取權的存取權檢閱。 這些控制項的範例可確實監督誰可以使用您組織的群組成員資格和應用程式。 組織可使用這些控制項有效地處理其治理、風險管理和合規性需求。

## <a name="create-and-manage-programs-and-their-controls"></a>建立和管理程式及其控制項
您可以將追蹤和收集不同用途的存取權檢閱簡化，方法是將它們組織到程式中。 每個存取權檢閱可連結至一個程式。 然後當您為稽核員準備報告時，您可以著重於特定方案範圍內的存取權檢閱。  具有全域管理員、使用者帳戶管理員、安全性系統管理員或安全性讀取者角色的使用者，可以看見程式和存取權檢閱結果。

若要查看程式清單，請移至[存取權檢閱頁面](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)，並選取 [程式集]。

一律會出現一個「預設程式」。 如果您具有全域管理員或使用者帳戶管理員角色，則可建立其他程式。 比方說，您可以針對每個合規性方案或業務目標建立一個程式。

如果您不再需要某個程式，且該程式未連結任何控制項，就可以將程式刪除。

## <a name="next-steps"></a>後續步驟

- [建立群組成員或應用程式存取的存取權檢閱](active-directory-azure-ad-controls-create-access-review.md)
- [擷取存取權檢閱的結果](active-directory-azure-ad-controls-retrieve-access-review.md)
