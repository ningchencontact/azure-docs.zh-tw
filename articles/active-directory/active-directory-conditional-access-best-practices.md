---
title: "Azure Active Directory 中條件式存取的最佳做法 | Microsoft Docs"
description: "了解您在設定條件式存取原則時應該知道的事件及應避免的動作。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 8c6707505a6331b53e06b1de60575dd3637ea477
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中條件式存取的最佳做法

此主題提供與您在設定條件式存取原則時應該知道的事件及應避免的動作相關的資訊。 在閱讀本主題前，您應熟悉 [Azure Active Directory 中條件式存取 (英文)](active-directory-conditional-access-azure-portal.md) 中所列的概念與術語

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="whats-required-to-make-a-policy-work"></a>讓原則運作的必要條件？

當您建立新的原則時，未選取任何使用者、群組、應用程式或存取控制項。

![雲端應用程式](./media/active-directory-conditional-access-best-practices/02.png)


若要讓您的原則運作，您必須設定下列各項：


|何事           | 方式                                  | 理由|
|:--            | :--                                  | :-- |
|**雲端應用程式** |您需要選取一或多個應用程式。  | 條件式存取原則的目標是要讓您微調授權的使用者如何存取您的應用程式。|
| **使用者和群組** | 您需要選取至少一個使用者或群組，已獲授權存取您選取的雲端應用程式。 | 永遠不會觸發未指派使用者和群組的條件式存取原則。 |
| **存取控制** | 您必須選取至少一個存取控制。 | 原則處理器需要知道滿足您的條件時該怎麼辦。|


除了這些基本需求，在許多情況下，您也應該設定條件。 雖然沒有設定條件時原則也可以運作，但是條件是微調應用程式存取權的驅動因素。


![雲端應用程式](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>如何評估指派？

所有指派邏輯上都會使用 **AND** 運算子。 如果您已設定多個指派，若要觸發原則，則必須滿足所有的指派。  

如果您需要設定一個位置條件，以套用至從您的組織網路外部進行的所有連線，您可以藉由下列方式達成︰

- 包含**所有位置**
- 排除**所有信任的 IP**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果您已在 Azure 傳統入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？  
Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果您已在 Intune Silverlight 入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？
Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果我已針對同一個使用者設定多個原則，則會發生什麼情況？  
針對每次登入，Azure Active Directory 會評估所有的原則，並確保在授與使用者存取權之前已符合所有的需求。


### <a name="does-conditional-access-work-with-exchange-activesync"></a>條件式存取是否適用於 Exchange ActiveSync？

是，您可以在條件式存取原則中使用 Exchange ActiveSync。


## <a name="what-you-should-avoid-doing"></a>您應該避免做的事

條件式存取架構可為您提供絕佳的組態彈性。 不過，絕佳的彈性也意謂著您應該先仔細地檢閱每個組態原則，然後才發行它，以避免產生不想要的結果。 在此情況下，您應該特別注意影響整個集合的指派，例如 **all users / groups / cloud apps**。

在您的環境中，應該避免使用下列組態：


**針對所有使用者、所有雲端應用程式：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。

- **需要符合規範的裝置** - 針對尚未註冊其裝置的使用者，此原則會封鎖所有存取，包括對 Intune 入口網站的存取。 如果您是沒有已註冊裝置的系統管理員，此原則會阻擋您回到 Azure 入口網站來變更此原則。

- **需要加入網域** - 如果您還沒有已加入網域的裝置，此原則也可能封鎖您組織中所有使用者的存取。


**針對所有使用者、所有雲端應用程式、所有裝置平台：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。



## <a name="policy-migration"></a>原則移轉

您應該考慮將移轉因為時，您已不在 Azure 入口網站中建立的原則：

- 您現在可以解決無法處理之前的案例。

- 您可以合併它們以減少必須管理的原則數目。   

- 您可以管理您所有單一中央位置的條件式存取原則。

- Azure 傳統入口網站將會停用。   


如需詳細資訊，請參閱[移轉 Azure 入口網站中的傳統原則](active-directory-conditional-access-migration.md)。


## <a name="next-steps"></a>後續步驟

如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。
