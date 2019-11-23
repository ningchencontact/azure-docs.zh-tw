---
title: Azure Active Directory Identity Protection 通知
description: 了解通知如何支援您的調查活動。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382162"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection 通知

Azure AD Identity Protection sends two types of automated notification emails to help you manage user risk and risk detections:

- 「偵測到具有風險的使用者」電子郵件
- 每週精選文章電子郵件

本文同時提供您這兩種通知電子郵件的概觀。

## <a name="users-at-risk-detected-email"></a>「偵測到具有風險的使用者」電子郵件

當偵測到具有風險的帳戶時，Azure AD Identity Protection 會產生主旨為**偵測到具有風險的使用者**的電子郵件警示來作為回應。 此電子郵件會包含 **[標幟為有風險的使用者](../reports-monitoring/concept-user-at-risk.md)** 報告的連結。 最佳做法是應立即調查具有風險的使用者。

The configuration for this alert allows you to specify at what user risk level you want the alert to be generated. The email will be generated when the user's risk level reaches what you have specified; however, you will not receive new users at risk detected email alerts for this user after they move to this user risk level. For example, if you set the policy to alert on medium user risk and your user John moves to medium risk, you will receive the users at risk detected email for John. However, you will not receive a second user at risk detected alert if John then moves to high risk or has additional risk detections.

![「偵測到具有風險的使用者」電子郵件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure users at risk detected alerts

如果您是系統管理員，您可以設定：

- **The user risk level that triggers the generation of this email** - By default, the risk level is set to “High” risk.
- **此電子郵件的收件者** - 收件者預設包含所有「全域管理員」。 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。
   - Optionally you can **Add additional emails to receive alert notifications** this feature is a preview and users defined must have the appropriate permissions to view the linked reports in the Azure portal.

Configure the users at risk email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Users at risk detected alerts**.

## <a name="weekly-digest-email"></a>每週精選文章電子郵件

The weekly digest email contains a summary of new risk detections.  
其中包括：

- 有風險的使用者
- 可疑的活動
- 偵測到的弱點
- Identity Protection 中相關報告的連結

![每週精選文章電子郵件](./media/howto-identity-protection-configure-notifications/400.png)

By default, recipients include all Global Admins. 「全域管理員」也可以新增其他「全域管理員」、「安全性管理員」、「安全性讀取者」作為收件者。

### <a name="configure-weekly-digest-email"></a>Configure weekly digest email

As an administrator, you can switch sending a weekly digest email on or off and choose the users assigned to receive the email.

Configure the weekly digest email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
