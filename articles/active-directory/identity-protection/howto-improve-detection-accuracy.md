---
title: 如何改善在 Azure Active Directory Identity Protection 中的偵測精確度 (已重新整理) | Microsoft Docs
description: 如何改善在 Azure Active Directory Identity Protection 中的偵測精確度 (已重新整理)。
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7724d69a9294b420ca061d5ad26ad64826372203
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517669"
---
# <a name="how-to-improve-the-detection-accuracy"></a>作法：改善偵測精確度 

Identity Protection 所提供的機制，可針對您環境中的風險偵測，向 Azure AD 提供意見反應。 若要提供意見反應，您可以確認偵測到的風險使用者或登入事件的狀態。 Microsoft 會使用此意見反應，對目前的風險偵測採取行動，並提高未來偵測的精確度。 


## <a name="what-is-detection"></a>什麼是偵測？

偵測是識別與您使用者帳戶相關的可疑活動流程。 Azure AD 可以偵測到的可疑活動稱為[風險事件](../reports-monitoring/concept-risk-events.md)。 該偵測流程是以調適性機器學習演算法和啟發學習法為基礎，以偵測使用者的風險事件。

偵測結果用來判斷使用者和登入是否處於風險中。 


## <a name="how-can-i-improve-the-detection-accuracy"></a>如何改善偵測精確度？

由於偵測是自動化的流程，因此，Azure AD 可能會回報誤判。 您可以將與偵測結果相關的意見反應提供給 Azure AD，以改善偵測精確度。

有三種方法可改善偵測精確度：確認遭入侵的登入、確認安全登入，以及解除使用者風險。 您可以透過以下報告執行此操作：

- **具風險登入的報告 -** 在具有風險登入的報告中，您可以確認登入是否安全或遭受入侵

- **具風險使用者報告 -** 在具風險使用者報告中，您可以解除使用者風險 

Azure AD 會處理您的意見反應，以提高偵測結果的精確度。 一般而言，您提供的意見反應會作為使用者風險或登入風險調查的一部分。 如需詳細資訊，請參閱[如何調查有風險的使用者和登入](howto-investigate-risky-users-signins.md)。


## <a name="confirm-compromised"></a>確認遭入侵

向 Azure AD 確認登入事件為遭入侵的訊號，表示登入未經身分識別擁有者授權。 當您選取「確認遭入侵」時，Azure AD 會

- 將受影響使用者的使用者風險提高為「高」。

- 幫助最佳化偵測風險事件的機器學習
 
- 執行額外措施，以進一步保護您的組織



若要確認遭入侵的登入：

- **具風險登入的報告** - 此選項可讓您確認一或多個遭入侵的登入事件。

    ![解除使用者風險](./media/howto-improve-detection-accuracy/07.png)

- **具風險登入報告的詳細資料檢視** - 此選項可讓您確認具風險登入報告中，所選取登入事件遭入侵的帳戶。 

    ![解除使用者風險](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>確認安全


向 Azure AD 確認登入事件為安全的訊號，表示登入**經過**個別身分識別擁有者授權。 當您選取「確認安全」時，Azure AD 會：

- 還原所選取登入的使用者風險比重

- 關閉基礎風險事件

- 幫助最佳化偵測風險事件的機器學習

- 執行額外措施，以進一步保護您的組織
 

若要確認安全登入：

- **具風險登入的報告** - 此選項可讓您確認一或多個安全登入的登入事件。

    ![解除使用者風險](./media/howto-improve-detection-accuracy/08.png)

- **具風險登入報告的詳細資料檢視** - 此選項可讓您確認具風險登入報告中，所選取登入事件的安全登入。 

    ![解除使用者風險](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>解除使用者風險

如果您已對風險使用者採取補救措施，或認為他們遭錯誤標示為有風險，您可以解除使用者的風險。 解除使用者風險時，會將使用者還原成非風險狀態。 選定使用者所有過去的風險登入和風險事件都將解除風險。


您可以解除報告的使用者風險：

- **具風險使用者報告** - 此選項可讓您解除一或多位選取之使用者的使用者風險。

    ![解除使用者風險](./media/howto-improve-detection-accuracy/02.png)

- **詳細資料檢視** - 此選項可讓您選定使用者風險報告中的使用者，解除其使用者風險。 

    ![解除使用者風險](./media/howto-improve-detection-accuracy/01.png)


**您應該知道的事情：**

- 您無法還原此動作。

- 可能需要幾分鐘才能完成此動作，所以您不須重新提交要求。

- 只有在由 AD 管理使用者認證的情況下，才能執行此動作。 



## <a name="best-practices"></a>最佳作法

如果使用者風險原則已封鎖使用者風險，解除使用者風險是一種解除封鎖的方法，而且由於未啟用密碼重設及/或 MFA，無法自我補救。 在此情況下，最好確保使用者註冊密碼重設和 MFA，以便他們未來能夠自我補救任何風險事件。


## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview-v2.md)。


