---
title: "Azure Active Directory Identity Protection 通知| Microsoft Docs"
description: "了解通知如何支援您的調查活動。"
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: bea21439afef4fda453732edffc84c62667dfe38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD Identity Protection 會傳送兩種自動化通知電子郵件，協助您管理使用者風險和風險事件：

- 在偵測到的風險電子郵件的使用者
- 每週精選文章電子郵件

本文章會提供您兩個通知電子郵件的概觀。


## <a name="users-at-risk-detected-email"></a>在偵測到的風險電子郵件的使用者

在偵測到的帳戶有風險的回應，Azure AD Identity Protection 會產生與電子郵件警示**使用者偵測到的風險**主旨為。 電子郵件中包含的連結**[使用者加上旗標的風險](active-directory-reporting-security-user-at-risk.md)**報表。 最佳做法，您應立即調查風險使用者。

![在偵測到的風險電子郵件的使用者](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>組態

身為管理員，您可以設定：

- **產生此電子郵件的觸發程序的風險層級**-根據預設，風險層級設定為 「 高 」 的風險。
- **這封電子郵件的收件者**-根據預設，收件者會包含所有全域管理員。 全域系統管理員也可以加入其他的全域系統管理員，安全性系統管理員安全性讀取器為收件者。  


若要開啟相關的對話方塊，請按一下**警示**中**設定**區段**Identity Protection**頁面。

![在偵測到的風險電子郵件的使用者](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>每週精選文章電子郵件

每週精選文章電子郵件包含新風險事件的摘要。  
其中包括：

- 有風險的使用者

- 可疑的活動

- 偵測到的弱點

- Identity Protection 中相關報告的連結

    ![補救](./media/active-directory-identityprotection-notifications/400.png "補救")

### <a name="configuration"></a>組態

身為管理員，您可以切換每週摘要電子郵件傳送。

![使用者風險](./media/active-directory-identityprotection-notifications/62.png "使用者風險")

若要開啟相關的對話方塊，請按一下**每週摘要**中**設定**區段**Identity Protection**頁面。

![在偵測到的風險電子郵件的使用者](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>請參閱

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
