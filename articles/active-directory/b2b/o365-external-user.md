---
title: Office 365 外部共用語 Azure Active Directory B2B 共同作業 | Microsoft Docs
description: 討論如何使用 O365 和 Azure Active Directory B2B 共同作業來與外部夥伴共用資源。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7e0a65e4a5807cb9a6b39feecfd2d5b2643ea4a9
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330656"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 外部共用與 Azure Active Directory B2B 共同作業

Office 365 (OneDrive、SharePoint Online、整合群組等) 中的外部共用與 Azure Active Directory (Azure AD) B2B 共同作業在技術上而言是相同的。 所有外部共用 (OneDrive/SharePoint Online 除外)，包括 Office 365 群組中的來賓都已使用 Azure AD B2B 共同作業邀請 API 來進行共用。

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Azure AD B2B 與 SharePoint Online 中的外部共用有何不同？

OneDrive/SharePoint Online 有個別的邀請管理員。 OneDrive/SharePoint Online 中的外部共用支援會在 Azure AD 開發其支援之前開始。 隨著時間的經過，OneDrive/SharePoint Online 外部共用已發展出數個功能，而且已有數百萬個使用者使用該產品的內建共用模式。 不過，OneDrive/SharePoint Online 外部共用的運作方式與 Azure AD B2B 共同作業的運作方式有些微差異。 您可以在[外部共用概觀](https://docs.microsoft.com/sharepoint/external-sharing-overview)中深入了解 OneDrive/SharePoint Online 外部共用。 此程序在下列各方面通常會與 Azure AD B2B 不同：

- OneDrive/SharePoint Online 會在使用者兌換其邀請之後，將使用者新增至目錄。 因此在兌換前，您不會在 Azure AD 入口網站中看到使用者。 如果其他網站同時邀請使用者，則會產生新的邀請。 不過，當您使用 Azure AD B2B 共同作業時，使用者會立即新增於邀請上，因而顯示在每個地方。

- OneDrive/SharePoint Online 中的兌換體驗與 Azure AD B2B 共同作業中的體驗有所差異。 在使用者兌換邀請之後，體驗看起來很類似。

- 您可以從 OneDrive/SharePoint Online 共用對話方塊挑選 Azure AD B2B 共同作業邀請的使用者。 在 OneDrive/SharePoint 邀請的使用者兌換其邀請之後，也會出現在 Azure AD 中。

- 授權需求不同。 對於每個付費的 Azure AD 授權，您最多可讓 5 位來賓使用者存取您的付費 Azure AD 功能。 若要深入了解授權，請參閱 [Azure AD B2B 授權](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance)以及[＜SharePoint Online 外部共用概觀＞中的「什麼是外部使用者？」](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-is-an-external-user)。

若要使用 Azure AD B2B 共同作業來管理 OneDrive/SharePoint Online 中的外部共用，請將 OneDrive/SharePoint Online 外部共用設定設為 [只允許與您組織目錄中現有的外部使用者共用]。 使用者可以移至外部共用網站，並從系統管理員新增的外部共同作業者之間選擇。 系統管理員可以透過 B2B 共同作業邀請 API 來新增外部共同作業者。


![OneDrive/SharePoint Online 外部共用設定](media/o365-external-user/odsp-sharing-setting.png)

## <a name="next-steps"></a>後續步驟

* [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
* [將 B2B 共同作業使用者新增至角色](add-guest-to-role.md)
* [委派 B2B 共同作業邀請](delegate-invitations.md)
* [動態群組與 B2B 共同作業](use-dynamic-groups.md)