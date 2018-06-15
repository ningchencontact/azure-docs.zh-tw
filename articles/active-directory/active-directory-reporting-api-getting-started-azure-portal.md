---
title: 開始使用 Azure AD 報告 API | Microsoft Docs
description: 如何開始使用 Azure Active Directory 報告 API
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: df0b672a07575a0d26fff89c90008043d02818dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589095"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>開始使用 Azure Active Directory 報告 API

Azure Active Directory 提供各種[報告](active-directory-reporting-azure-portal.md)給您。 這些報告的資料對您的應用程式 (例如 SIEM 系統、稽核和商業智慧工具) 可能非常有用。 

使用 Azure AD 報告 API，您可透過一組以 REST 為基礎的 API 為您提供資料的程式設計存取方式。 您可以從各種程式設計語言和工具呼叫這些 API。

本文為您提供藍圖，以便使用相關 API 存取報告資料。

如果您遇到問題，請參閱[如何取得 Azure Active Directory 支援](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)。


## <a name="prerequisites"></a>先決條件

若要存取報告 API，即使您打算使用指令碼來存取此 API，也必須：

1. 指派角色 (安全性讀取者、安全性管理員或全域管理員)
2. 註冊應用程式
3. 授與權限
4. 收集組態設定


 
如需詳細指示，請參閱[存取 Azure Active Directory 報告 API 的必要條件](active-directory-reporting-api-prerequisites-azure-portal.md)。


## <a name="recommendation"></a>建議 

如果您打算在不需使用者介入的情況下擷取報告資料，您應該考慮使用 Azure AD 報告 API 搭配憑證。

如需詳細指示，請參閱[使用 Azure AD 報告 API 搭配憑證來取得資料](active-directory-reporting-api-with-certificates.md)。


## <a name="explore"></a>瀏覽

取得報告 API 的第一印象：
   
   - [使用稽核 API 的範例](active-directory-reporting-api-audit-samples.md) 
 
   - [使用登入活動報告 API 的範例](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>自訂  

建立您自己的解決方案： 
   
   - [使用稽核 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [使用登入活動報告 API 參考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



