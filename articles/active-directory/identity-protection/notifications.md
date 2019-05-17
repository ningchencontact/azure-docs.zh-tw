---
title: Azure Active Directory Identity Protection 通知| Microsoft Docs
description: 了解通知如何支援您的調查活動。
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0065ec03695ee977133ae2ec43aafba7d5bfff78
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784350"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD Identity Protection 會傳送兩種自動化通知電子郵件，協助您管理使用者風險和風險事件：

- 「偵測到具有風險的使用者」電子郵件
- 每週精選文章電子郵件

本文同時提供您這兩種通知電子郵件的概觀。


## <a name="users-at-risk-detected-email"></a>「偵測到具有風險的使用者」電子郵件

當偵測到具有風險的帳戶時，Azure AD Identity Protection 會產生主旨為**偵測到具有風險的使用者**的電子郵件警示來作為回應。 此電子郵件會包含**[標幟為有風險的使用者](../reports-monitoring/concept-user-at-risk.md)** 報告的連結。 最佳做法是應立即調查具有風險的使用者。

此警示的組態可讓您指定哪些使用者風險層級中，您想要產生警示。 使用者風險層級可讓您達到指定; 時，將會產生電子郵件不過，您不會收到新的使用者，在偵測到的風險這位使用者的電子郵件警示，它們移動到這個使用者風險層級之後。 例如，如果您設定原則來警示使用者風險和您 John 移至中度風險的使用者，您會收到上偵測到的風險電子郵件的使用者 John。 不過，您不會收到第二個使用者在偵測到的風險的警示，如果 John 然後移至高風險，或有額外風險事件。

![「偵測到具有風險的使用者」電子郵件](./media/notifications/01.png)


### <a name="configuration"></a>組態

如果您是系統管理員，您可以設定：

- **觸發產生此電子郵件的使用者風險層級**-根據預設，風險層級設定為 「 高 」 風險。
- **此電子郵件的收件者** - 收件者預設包含所有「全域管理員」。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。  


若要開啟相關的對話方塊，請在 [Identity Protection] 頁面的 [設定] 區段中，按一下 [警示]。

![「偵測到具有風險的使用者」電子郵件](./media/notifications/05.png)


## <a name="weekly-digest-email"></a>每週精選文章電子郵件

每週精選文章電子郵件包含新風險事件的摘要。  
其中包括：

- 具有風險的使用者

- 可疑的活動

- 偵測到的弱點

- Identity Protection 中相關報告的連結

    ![補救](./media/notifications/400.png "補救")

### <a name="configuration"></a>組態

如果您是系統管理員，您可以關閉每週摘要電子郵件的傳送。

![使用者風險](./media/notifications/62.png "使用者風險")

若要開啟相關的對話方塊，請在 [Identity Protection] 頁面的 [設定] 區段中，按一下 [每週摘要]。

![「偵測到具有風險的使用者」電子郵件](./media/notifications/04.png)


## <a name="see-also"></a>請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
