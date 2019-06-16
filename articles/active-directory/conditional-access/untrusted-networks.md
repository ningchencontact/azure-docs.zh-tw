---
title: 如何使用 Azure Active Directory (Azure AD) 條件式存取不受信任的網路存取要求 multi-factor authentication (MFA) |Microsoft Docs
description: 了解如何設定 Azure Active Directory (Azure AD) 中的條件式存取原則的存取嘗試來自不受信任的網路。
services: active-directory
keywords: 條件式存取應用程式，與 Azure AD，安全地存取公司資源，條件式存取原則的條件式存取
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa9760c44df13c33687c279478935600325dd085
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111871"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>作法：使用條件式存取不受信任的網路存取要求 MFA   

Azure Active Directory (Azure AD) 可讓使用者從任何位置單一登入到裝置、應用程式和服務。 您的使用者不僅可以從組織的網路存取雲端應用程式，還可以從任何未受信任的網際網路位置存取。 從未受信任的網路存取的常見最佳做法是要求多重要素驗證 (MFA)。

本文提供您要設定條件式存取原則，要求使用 MFA 進行受信任的網路存取所需的資訊。 

## <a name="prerequisites"></a>必要條件

本文假設您已熟悉以下各項： 

- [基本概念](overview.md)的 Azure AD 條件式存取 
- [最佳做法](best-practices.md)在 Azure 入口網站中設定條件式存取原則



## <a name="scenario-description"></a>案例描述

在從您組織的網路登入的情況下，若要達到安全性和生產力之間的最佳平衡，只要求密碼應該就足夠了。 不過，對於來自不受信任之網路位置的存取，登入之使用者不是合法使用者的風險會變高。 若要解決這個問題，您可以封鎖來自不受信任之網路的存取。 或者，您也可以要求進行多重要素驗證 (MFA)，保證確實是其他合法的帳戶擁有者想登入網路。 

您可以使用 Azure AD 條件式存取，來解決與單一原則可授與存取這項需求： 

- 將存取權授與所選的雲端應用程式

- 將存取權授與所選使用者和群組  

- 從以下位置發出存取嘗試時 

- 當存取的來源是： 

    - 不被信任的位置


## <a name="implementation"></a>實作

此案例的挑戰是要轉譯*來自不受信任的網路位置存取*到條件式存取條件。 在條件式存取原則中，您可以設定[位置條件](location-condition.md)網路位置相關的處理案例。 位置條件可讓您選取具名位置，它是 IP 位址範圍、國家和地區的邏輯群組。  

一般而言，您的組織擁有一或多個位址範圍，例如 199.30.16.0 - 199.30.16.24。
若要設定具名位置，請：

- 指定這個範圍 (199.30.16.0/24) 

- 指派一個描述性名稱，例如**公司網路** 


若不想嘗試定義所有不被信任的位置，您可以：

- 包含任何位置 

    ![條件式存取](./media/untrusted-networks/02.png)

- 排除所有信任的位置 

    ![條件式存取](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>原則部署

使用本文中所述方法時，您現在可以設定條件式存取原則的受信任的位置。 若要確定您的原則會如預期般運作，建議的最佳做法是先測試，再推出到生產環境。 在理想情況下，可以使用測試租用戶來驗證您的新原則是否如預期般運作。 如需詳細資訊，請參閱[如何部署新原則](best-practices.md#how-should-you-deploy-a-new-policy)。 



## <a name="next-steps"></a>後續步驟

如果您想要深入了解條件式存取，請參閱[什麼是 Azure Active Directory 中的條件式存取？](../active-directory-conditional-access-azure-portal.md)
