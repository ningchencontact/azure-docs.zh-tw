---
title: B2B 共同作業中的邀請兌換 - Azure Active Directory | Microsoft Docs
description: 描述使用者的 Azure AD B2B 共同作業邀請兌換經驗，包括隱私權條款的協議。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052518"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 共同作業邀請兌換

這篇文章說明來賓使用者可以存取您的資源和同意程序將會遇到的方式。 如果您傳送至客體的邀請電子郵件，邀請包含來賓可以兌換的連結即可存取您的應用程式或入口網站。 邀請電子郵件只是來賓可以取得資源的存取權的方式。 或者，您可以將來賓新增至您的目錄，並提供它們的直接連結至入口網站或您想要共用的應用程式。 不論他們使用的方法，來賓會引導您完成第一次同意程序。 此程序可確保您允許來賓同意隱私權條款，並接受任何[使用規定](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)您已設定。

當您新增至您的目錄的來賓使用者時，來賓使用者帳戶已同意狀態 （可在 PowerShell 中），一開始會設**PendingAcceptance**。 此設定會保留直到客體接受您的邀請，並同意您的隱私權原則及使用規定。 在那之後，同意狀態會變更為**Accepted**，並同意頁面不會向客體。

## <a name="redemption-through-the-invitation-email"></a>透過邀請電子郵件兌換

當您將來賓使用者加入您的目錄所[使用 Azure 入口網站](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)，邀請電子郵件會傳送至處理序中的來賓。 您也可以選擇傳送邀請電子郵件，當您準備[使用 PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)將來賓使用者新增至您的目錄。 以下是體驗的客體的描述，他們必須兌換電子郵件中的連結時。

1. 客體會接收[邀請電子郵件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)，從傳送**Microsoft 邀請**。
2. 客體選取**開始**電子郵件中。
3. 如果在同盟的組織中沒有 Azure AD 帳戶、 Microsoft 帳戶 (MSA) 或電子郵件帳戶的來賓，會提示他們建立 MSA (除非[單次密碼](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)啟用功能時，這並不需要 MSA).
4. 客體引導[同意體驗](#consent-experience-for-the-guest)如下所述。

## <a name="redemption-through-a-direct-link"></a>透過直接連結兌換

替代方案的邀請電子郵件中，您可以讓來賓的直接連結至您的應用程式或入口網站。 必須先將來賓使用者新增至您的目錄，透過[Azure 入口網站](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)或是[PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)。 然後您可以使用任一[可自訂的方式，來部署應用程式給使用者](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)，包括直接登入連結。 當來賓會使用直接連結，而不是邀請電子郵件時，他們將仍會引導您完成第一次同意體驗。

> [!IMPORTANT]
> 直接連結必須是租用戶專屬。 換句話說，它必須包含租用戶識別碼，或已驗證網域，讓來賓可以驗證您的租用戶，共用的應用程式所在的位置中。 常見的 URL，如 https://myapps.microsoft.com 並不適用於來賓中，因為它會重新導向至其主租用戶中進行驗證。 以下是與租用戶內容的直接連結的一些範例：
 > - 應用程式存取面板： https://myapps.microsoft.com/?tenantid=&lt ; 租用戶識別碼&gt; 
 > - 已驗證網域的應用程式存取面板： https://myapps.microsoft.com/&lt ; 已驗證網域&gt;
 > - Azure 入口網站： https://portal.azure.com/&lt ; 租用戶識別碼&gt;
 > - 個別的應用程式： 了解如何使用[直接登入連結](../manage-apps/end-user-experiences.md#direct-sign-on-links)

有某些情況下，透過直接連結中建議到邀請電子郵件。 如果這些特殊的情況下會對組織而言重要的我們建議您在使用方法，還是將傳送邀請電子郵件邀請使用者：
 - 在同盟的組織沒有 Azure AD 帳戶、 MSA 或電子郵件帳戶使用者。 除非您要使用的單次密碼功能，客體需要兌換邀請函電子郵件，會引導您完成建立 MSA 的步驟。
 - 受邀的使用者物件有時可能因為與連絡人物件 (例如 Outlook 連絡人物件) 衝突，而沒有電子郵件地址。 在此情況下，使用者必須按一下邀請電子郵件中的兌換 URL。
 - 使用者可以使用受邀的電子郵件地址別名登入。 (別名是與電子郵件帳戶相關聯的其他電子郵件位址。)在此情況下，使用者必須按一下邀請電子郵件中的兌換 URL。

## <a name="consent-experience-for-the-guest"></a>客體的同意體驗

當來賓登入時存取第一次以合作夥伴組織中的資源時，它們會引導您完成下列頁面。 

1. 客體評論**檢閱權限**頁面描述邀請方組織的隱私權聲明。 使用者必須**接受**根據邀請組織的隱私權原則，若要繼續其資訊的使用。

   ![顯示 [檢閱權限] 頁面的螢幕擷取畫面](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 有關身為租用戶管理員的您如何連結至貴組織隱私權聲明的詳細資訊，請參閱[操作說明：在 Azure Active Directory 中新增貴組織的隱私權資訊](https://aka.ms/adprivacystatement)。

2. 如果設定的使用規定，來賓會開啟並檢閱使用條款，，然後選取**接受**。 

   ![顯示新增使用規定的螢幕擷取畫面](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > 您可以設定，請參閱[使用條款](../governance/active-directory-tou.md)中**管理** > **組織關係** > **使用條款**.

3. 除非另有指定，客體會重新導向列出客體可存取的應用程式的應用程式存取面板。

   ![顯示應用程式存取面板的螢幕擷取畫面](media/redemption-experience/myapps.png) 

您目錄中，客體會**接受邀請**值會變成**是**。 如果已建立 MSA，客體**來源**示範**的 Microsoft 帳戶**。 如需來賓使用者帳戶內容的詳細資訊，請參閱[的 Azure AD B2B 共同作業使用者屬性](user-properties.md)。 

## <a name="next-steps"></a>後續步驟

- [何謂 Azure AD B2B 共同作業？](what-is-b2b.md)
- [在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](add-users-administrator.md)
- [資訊工作者如何將 B2B 共同作業使用者新增到 Azure Active Directory？](add-users-information-worker.md)
- [使用 PowerShell 新增 Azure Active Directory B2B 共同作業使用者](customize-invitation-api.md#powershell)
- [以來賓使用者的身分從組織離職](leave-the-organization.md)
