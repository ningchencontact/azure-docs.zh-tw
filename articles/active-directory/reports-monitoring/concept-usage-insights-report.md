---
title: 在 Azure Active Directory 入口網站中報告的使用量和深入解析 |Microsoft Docs
description: 在 Azure Active Directory 入口網站中的使用方式和深入解析報告簡介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806352"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>在 Azure Active Directory 入口網站中的使用量和 insights 報表

使用 [使用量和深入解析] 報告中，您可以取得登入資料的應用程式為中心的檢視。 您可以找到下列問題的答案：

*   什麼是頂端使用我的組織中的應用程式嗎？
*   哪些應用程式是否有最失敗的登入？ 
*   主要登入錯誤的每個應用程式有哪些？

## <a name="prerequisites"></a>先決條件 

若要從使用量和深入解析報告存取資料，您需要：

* Azure AD 租用戶
* Azure AD premium (P1 / 「 進階 P2) 授權，以檢視登入資料
* 全域管理員、 安全性系統管理員、 安全性讀取者或報表讀取者角色中的使用者。 此外，任何使用者 （非系統管理員） 可以存取自己的登入。 

## <a name="access-the-usage-and-insights-report"></a>存取使用量和深入解析報告

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 選取正確的目錄，然後選取**Azure Active Directory** ，然後選擇**企業應用程式**。
3. 從**活動**區段中，選取**使用量和 insights**開啟報表。 

![使用量和深入解析報告](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>使用報表

使用量和 insights 報告中嘗試，會顯示以一或多個符號的應用程式清單，並可讓您成功登入失敗的登入及成功率的數字排序。

在清單底部的 負載可讓您檢視頁面上的其他應用程式。 您可以選取要檢視已使用的範圍內的所有應用程式的日期範圍。

您也可以設定特定的應用程式的焦點。 選取 **檢視登入活動**若要查看活動中的正負號，經過一段時間的應用程式，以及主要的錯誤。  

當您的應用程式使用量圖表中選取一天時，您會取得應用程式的登入活動的詳細的清單。  

![使用量和深入解析報告](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>後續步驟

* [登入報告](concept-sign-ins.md)