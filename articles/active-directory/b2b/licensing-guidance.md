---
title: Azure Active Directory B2B 共同作業授權指導方針 | Microsoft Docs
description: Azure Active Directory B2B 共同作業不需要 Azure AD 付費授權，但您也可以取得付費功能給 B2B 來賓使用者
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.openlocfilehash: d80794511f334cd6dc5af418e24fc774b7d8728f
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867505"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 共同作業授權指導方針

使用 Azure Active Directory (Azure AD) 企業對企業 (B2B) 共同作業時，您可以邀請「外部使用者」(或「來賓使用者」) 來使用您的付費 Azure AD 服務。 針對您指派給使用者的每個付費 Azure AD 授權，在「外部使用者額度」下，您最多可以邀請 5 位使用者。

來賓使用者係指不是貴組織或貴組織任何關係企業之成員的人員。 來賓使用者會依他們與貴組織的關聯性來定義，而不是依他們用來登入的認證來定義。 事實上，來賓使用者可以使用外部身分識別來登入，也可以使用貴組織所擁有的認證來登入。

以下使用者「不是」來賓使用者：
- 您的員工、現場約聘人員或現場代理人員
- 您關係企業的員工、現場約聘人員或現場代理人員

系統會根據 1:5 的比例來自動計算和回報 B2B 來賓使用者授權。 目前無法直接將 B2B 來賓使用者授權指派給來賓使用者。

在某些情況下，使用 1:5「外部使用者額度」時，不會回報來賓使用者。 如果來賓使用者在自己的組織中已經有付費的 Azure AD 授權，該使用者就不會使用您的 B2B 來賓使用者授權。 此外，來賓使用者無須符合任何額外的授權需求，即可使用免費的 Azure AD 功能。 即使您沒有任何付費的 Azure AD 授權，來賓使用者也可以存取免費的 Azure AD 功能。 

## <a name="examples-calculating-guest-user-licenses"></a>範例：計算來賓使用者授權
在您判斷出有多少來賓使用者需要存取您的付費 Azure AD 服務之後，請確定您有足夠的 Azure AD 付費授權可涵蓋所需 1:5 比例的來賓使用者。 這裡有一些範例：

- 您想要邀請 100 位來賓使用者使用您的 Azure AD 應用程式或服務，並想要對所有來賓使用者指派存取管理和佈建。 此外，您也想要針對這些來賓使用者中的 50 位，要求使用 MFA 和條件式存取。 若要涵蓋這個組合，您將需要 10 個 Azure AD Basic 授權及 10 個 Azure AD Premium P1 授權。 如果您打算對來賓使用者使用「身分識別保護」功能，則需要同樣 1:5 比例的 Azure AD Premium P2 授權，才能這些使用者。
- 您想要邀請 60 位全都必須使用 MFA 的來賓使用者，因此您必須至少有 12 個 Azure AD Premium P1 授權。 您有 10 個具備 Azure AD Premium P1 授權的員工，在 1:5 授權比例下，這將最多可允許 50 位來賓使用者。 您將需要購買 2 個額外的 Premium P1 授權，才能涵蓋 10 位額外的來賓使用者。

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>使用 B2B 共同作業 API 以邀請來自您關係企業的使用者

根據定義，B2B 來賓使用者係指您所邀請來使用付費 Azure AD 應用程式和服務的「外部使用者」。 貴公司或您其中一個關係企業的員工、現場約聘人員或現場代理人員即使使用 B2B 功能，也不符合進行 B2B 共同作業的資格。 這裡有一些範例： 
- 您想要使用外部認證 (例如社交身分識別) 來邀請身為貴組織員工的使用者。 此案例不符合授權需求規範，因此並不允許。 外部認證並不會讓員工成為「外部使用者」。  
- 您想要使用 B2B API 來邀請來自您其中一個組織關係企業的使用者。 雖然此案例使用 B2B API 來邀請該使用者，但系統並不會將其視為 B2B 共同作業。 這不符合授權需求，因為來自您關係企業的使用者並不是「外部使用者」。 

在上述兩個案例中，較佳的解決方案是使用 B2B API 來邀請這些使用者作為成員 (invitedUserType = Member)，然後為他們每個人指派 Azure AD 授權。 

## <a name="next-steps"></a>後續步驟

請參閱下列有關 Azure AD B2B 共同作業的資源：

* [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
* [Azure Active Directory B2B 共同作業常見問題 (FAQ)](faq.md)
