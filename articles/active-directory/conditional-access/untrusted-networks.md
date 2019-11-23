---
title: Require MFA from untrusted networks - Azure Active Directory
description: Learn how to configure a Conditional Access policy in Azure Active Directory (Azure AD) to for access attempts from untrusted networks.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379995"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>How to: Require MFA for access from untrusted networks with Conditional Access   

Azure Active Directory (Azure AD) 可讓使用者從任何位置單一登入到裝置、應用程式和服務。 您的使用者不僅可以從組織的網路存取雲端應用程式，還可以從任何未受信任的網際網路位置存取。 從未受信任的網路存取的常見最佳做法是要求多重要素驗證 (MFA)。

This article gives you the information you need to configure a Conditional Access policy that requires MFA for access from untrusted networks. 

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉以下各項： 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>案例描述

在從您組織的網路登入的情況下，若要達到安全性和生產力之間的最佳平衡，只要求密碼應該就足夠了。 不過，對於來自不受信任之網路位置的存取，登入之使用者不是合法使用者的風險會變高。 若要解決這個問題，您可以封鎖來自不受信任之網路的存取。 或者，您也可以要求進行多重要素驗證 (MFA)，保證確實是其他合法的帳戶擁有者想登入網路。 

With Azure AD Conditional Access, you can address this requirement with a single policy that grants access: 

- 將存取權授與所選的雲端應用程式
- 將存取權授與所選使用者和群組  
- 從以下位置發出存取嘗試時 
- 當存取的來源是： 
   - 不被信任的位置

## <a name="implementation"></a>實作

The challenge of this scenario is to translate *access from an untrusted network location* into a Conditional Access condition. In a Conditional Access policy, you can configure the [locations condition](location-condition.md) to address scenarios that are related to network locations. 位置條件可讓您選取具名位置，它是 IP 位址範圍、國家和地區的邏輯群組。  

Typically, your organization owns one or more address ranges, for example, 199.30.16.0 - 199.30.16.15.
若要設定具名位置，請：

- Specifying this range (199.30.16.0/28) 
- 指派一個描述性名稱，例如**公司網路** 

若不想嘗試定義所有不被信任的位置，您可以：

- 包含任何位置 

   ![條件式存取](./media/untrusted-networks/02.png)

- 排除所有信任的位置 

   ![條件式存取](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>原則部署

With the approach outlined in this article, you can now configure a Conditional Access policy for untrusted locations. 若要確定您的原則會如預期般運作，建議的最佳做法是先測試，再推出到生產環境。 在理想情況下，可以使用測試租用戶來驗證您的新原則是否如預期般運作。 如需詳細資訊，請參閱[如何部署新原則](best-practices.md#how-should-you-deploy-a-new-policy)。 

## <a name="next-steps"></a>後續步驟

If you would like to learn more about Conditional Access, see [What is Conditional Access in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
