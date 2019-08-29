---
title: Azure Active Directory Identity Protection 通知| Microsoft Docs
description: 了解通知如何支援您的調查活動。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9da0552e3ccc707c6b2f228b402f4e9db7dafee3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125713"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD Identity Protection 會傳送兩種類型的自動化通知電子郵件, 協助您管理使用者風險和風險偵測:

- 「偵測到具有風險的使用者」電子郵件
- 每週精選文章電子郵件

本文同時提供您這兩種通知電子郵件的概觀。

## <a name="users-at-risk-detected-email"></a>「偵測到具有風險的使用者」電子郵件

當偵測到具有風險的帳戶時，Azure AD Identity Protection 會產生主旨為**偵測到具有風險的使用者**的電子郵件警示來作為回應。 此電子郵件會包含 **[標幟為有風險的使用者](../reports-monitoring/concept-user-at-risk.md)** 報告的連結。 最佳做法是應立即調查具有風險的使用者。

此警示的設定可讓您指定要產生警示的使用者風險層級。 當使用者的風險層級達到您指定的值時, 就會產生電子郵件;不過, 在使用者移至此使用者風險層級之後, 您將不會收到有風險的新使用者偵測到此使用者的電子郵件警示。 例如, 如果您將原則設定為針對中型使用者風險發出警示, 而您的使用者 John 移至中等風險, 您會收到 John 偵測到有風險的使用者電子郵件。 不過, 如果 John 接著進入高風險或有額外的風險偵測, 您就不會收到第二位有風險的使用者偵測到警示。

![「偵測到具有風險的使用者」電子郵件](./media/notifications/01.png)

### <a name="configuration"></a>組態

如果您是系統管理員，您可以設定：

- **觸發此電子郵件產生的使用者風險層**級-根據預設, 風險層級會設定為「高」風險。
- **此電子郵件的收件者** - 收件者預設包含所有「全域管理員」。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。  

若要開啟相關的對話方塊，請在 [Identity Protection] 頁面的 [設定] 區段中，按一下 [警示]。

![「偵測到具有風險的使用者」電子郵件](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>每週精選文章電子郵件

每週摘要電子郵件包含新風險偵測的摘要。  
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

## <a name="see-also"></a>另請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
