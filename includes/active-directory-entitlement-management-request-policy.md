---
title: 包含檔案
description: 包含檔案
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 6f2b5eb96eeb1c4b7d07219d5fe54a8a0ca9e28a
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2019
ms.locfileid: "73412951"
---
## <a name="for-users-in-your-directory"></a>適用于您目錄中的使用者

如果您想要允許目錄中的使用者能夠要求此存取套件，請遵循下列步驟。 定義要求原則時，您可以指定個別使用者，或更常用的使用者群組。 例如，您的組織可能已經有一個群組，例如 [**所有員工**]。  如果為可要求存取權的使用者在原則中新增該群組，則該群組的任何成員都可以要求存取權。

1. 在 [可要求存取的使用者] 區段中，按一下 [您目錄中的使用者]。

    當您選取此選項時，會出現新的選項，以進一步精簡您的目錄中可以要求此存取封裝的人員。

    ![存取套件-要求-適用于您目錄中的使用者](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定使用者和群組** | 如果您只想要您指定的目錄中的使用者和群組能夠要求此存取封裝，請選擇此選項。 |
    | **所有成員（不包括來賓）** | 如果您要讓目錄中的所有成員使用者都能夠要求此存取封裝，請選擇此選項。 此選項不包含任何您可能已受邀進入目錄的來賓使用者。 |
    | **所有使用者（包括來賓）** | 如果您要讓目錄中的所有成員使用者和來賓使用者都能夠要求此存取封裝，請選擇此選項。 |

    來賓使用者會參考已受邀使用[AZURE AD B2B](../articles/active-directory/b2b/what-is-b2b.md)的目錄中的外部使用者。 如需成員使用者與來賓使用者之間差異的詳細資訊，請參閱[Azure Active Directory 中的預設使用者權力為何？](../articles/active-directory/fundamentals/users-default-permissions.md)。

1. 如果您選取 [**特定使用者和群組**]，請按一下 [**新增使用者和群組**]。

1. 在 [選取使用者和群組] 窗格中，選取您想要新增的使用者和群組。

    ![存取套件-要求-選取使用者和群組](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. 按一下 [**選取**] 以新增使用者和群組。

1. 向下跳至 [[核准](#approval)] 區段。

## <a name="for-users-not-in-your-directory"></a>針對不在您目錄中的使用者

如果您想要讓不在您目錄中的使用者能夠要求此存取套件，請遵循下列步驟。 **不在您目錄中的使用者**指的是另一個 Azure AD 目錄或網域中的使用者，而且可能尚未被邀請到您的目錄中。 Azure AD 目錄必須設定為允許共同作業**限制**中的邀請。 如需詳細資訊，請參閱[啟用 B2B 外部共同作業和管理可以邀請來賓的人員](../articles/active-directory/b2b/delegate-invitations.md)。

> [!NOTE]
> 系統會針對尚未在您的目錄中核准或自動核准要求的使用者，建立來賓使用者帳戶。 系統會邀請來賓，但不會收到邀請電子郵件。 相反地，他們會在其存取套件指派傳遞時收到電子郵件。 根據預設，當該來賓使用者不再具有任何存取套件指派時，由於其上次指派已過期或已取消，所以該來賓使用者帳戶將會遭到封鎖而無法登入，且後續將會刪除。 如果您想要讓來賓使用者無限期地保留在您的目錄中，即使他們沒有任何存取套件指派，您也可以變更您的權利管理設定。 如需有關來賓使用者物件的詳細資訊，請參閱[AZURE ACTIVE DIRECTORY B2B 共同作業使用者的屬性](../articles/active-directory/b2b/user-properties.md)。

1. 在 [**可以要求存取權的使用者**] 區段中，按一下 [**針對不在您的目錄中的使用者**]。

    當您選取此選項時，會出現新的選項。

    ![存取套件-要求-適用于不在您目錄中的使用者](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. 選取下列其中一個選項：

    |  |  |
    | --- | --- |
    | **特定的已連線組織** | 如果您想要從系統管理員先前新增的組織清單中選取，請選擇此選項。 來自所選組織的所有使用者都可以要求此存取封裝。 |
    | **所有已連線的組織** | 如果您想要所有已連線組織的所有使用者都能夠要求此存取套件，請選擇此選項。 |

    已連線的組織是與您有關聯性的外部 Azure AD 目錄或網域。

1. 如果您選取了 [**特定的連線組織**]，請按一下 [**新增目錄**]，從您的系統管理員先前新增的已連線組織清單中選取。

1. 輸入 [名稱] 或 [功能變數名稱]，以搜尋先前連線的組織。

    ![存取套件-要求-選取目錄](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    如果您想要與共同作業的組織不在清單中，您可以要求系統管理員將其新增為已連線的組織。 如需詳細資訊，請參閱[新增已連線的組織](../articles/active-directory/governance/entitlement-management-organization.md)。

1. 選取所有已連線的組織之後，請按一下 [**選取**]。

    > [!NOTE]
    > 來自所選已連線組織的所有使用者都將能夠要求此存取封裝。 這包括從與組織相關聯的所有子域 Azure AD 的使用者，除非 Azure B2B 允許或拒絕清單已封鎖這些網域。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../articles/active-directory/b2b/allow-deny-list.md)。

1. 向下跳至 [[核准](#approval)] 區段。

## <a name="none-administrator-direct-assignments-only"></a>無（僅限系統管理員直接指派）

如果您想要略過存取要求，並允許系統管理員直接將特定使用者指派給此存取套件，請遵循下列步驟。 使用者不需要要求存取封裝。 您仍然可以設定生命週期設定，但不會有任何要求設定。

1. 在 [**可要求存取的使用者**] 區段中，按一下 [**無] （僅限系統管理員直接指派**。

    ![存取套件-要求-無系統管理員直接指派](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    建立存取套件之後，您可以直接將特定的內部和外部使用者指派給存取封裝。 如果您指定外部使用者，將會在您的目錄中建立來賓使用者帳戶。 如需直接指派使用者的相關資訊，請參閱[View、add 和 remove access package 的指派](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)。

1. 向下跳至 [[啟用要求](#enable-requests)] 區段。

## <a name="approval"></a>核准

在 [核准] 區段中，您可以指定使用者要求此存取套件時是否需要核准。 核准設定的工作方式如下：

- 只有其中一個選取的核准者或回溯核准者需要核准要求。 不需要來自所有核准者的核准。
- 核准決策是以第一次審核要求的核准者為基礎。

請遵循下列步驟來指定您先前選取之使用者的核准設定。

1. 若要要求核准所選使用者的要求，請將 [**需要核准**] 切換設定為 **[是]** 。 若要自動核准要求，請將切換設定為 [**否**]。

1. 若要要求使用者提供理由來要求存取封裝，請將 [**需要要求者理由**] 切換為 **[是]** 。

    ![存取套件-要求-核准設定](./media/active-directory-entitlement-management-request-policy/approval.png)

### <a name="single-stage-approval"></a>單一階段核准

1. 針對核准者，選取 [**管理員] 作為核准者**，或**選擇 [特定核准者**]。

    ![存取套件-要求-單一階段設定](./media/active-directory-entitlement-management-request-policy/approval-single-stage.png)

1. 如果您選取 [管理員] 作為 [核准者]，請按一下 [**新增**回溯]，在您的目錄中選取一或多個使用者或群組，以作為版權管理找不到管理員時的回溯核准者。

    管理員是由 Azure AD 的使用者設定檔中的**manager**屬性所決定。 如需詳細資訊，請參閱[使用 Azure Active Directory 新增或更新使用者的設定檔資訊](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)。

1. 如果您選取 [選擇特定核准者]，請按一下 [**新增核准**者]，在您的目錄中選取核准者的一或多個使用者或群組。

1. 在 [必須在幾**天內進行決策？** ] 中，指定核准者審查此存取套件要求的天數。

    如果要求未在這段時間內核准，則會自動拒絕。 使用者必須提交存取套件的另一個要求。

1. 若要要求使用者提供理由來要求存取套件，請將 [**需要核准者理由**] 設定為 **[是]** 。

    其他核准者和要求者可以看到理由。

### <a name="alternate-approvers"></a>替代核准者

除了指定可核准要求的主要核准者以外，您還可以指定替代的核准者。 這將有助於確保要求會在到期之前核准或拒絕（timeout）。

藉由指定替代核准者，在主要核准者無法核准或拒絕要求的情況下，會根據您在設定原則期間指定的轉送排程，將擱置的要求轉寄給其他核准者。 他們會收到電子郵件，以核准或拒絕擱置中的要求。

將要求轉送到替代核准者之後，主要核准者仍然可以核准或拒絕要求。 替代核准者會使用與主要核准者相同的我的存取權網站來核准或拒絕擱置中的要求。

我們可以將人員或群組列出為主要核准者和替代核准者。 請確定您列出不同的人員集合，使其成為主要核准者和替代核准者。
例如，如果您將 Alice 和 Bob 列為主要核准者，請將 Carol 和 Dave 列出為替代核准者。 使用下列步驟，將替代核准者新增至存取套件：

1. 按一下 [**顯示先進的要求設定**]。

    ![存取套件-原則-顯示 advanced 要求設定](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. 設定**是否未採取任何動作，轉寄給替代核准者？** 切換為 **[是]** 。

1. 按一下 [**新增替代核准者**]，並從清單中選取替代核准者。

    ![存取套件-原則-新增替代的核准者](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. 在 [在**幾天後向前前往替代核准者**] 方塊中，放入核准者核准或拒絕要求的天數。 如果沒有核准者在要求期間之前核准或拒絕要求，則要求會過期（超時），而且使用者必須提交存取封裝的另一個要求。 

    在要求持續時間達到半生命後，才可將要求轉寄給替代核准者一天。 在此範例中，要求的持續時間為14天。 這表示要求持續時間到達第7天的半生命週期。 因此，要求可以轉送到第8天之前。 此外，在要求持續時間的最後一天，無法將要求轉寄給替代的核准者。 因此，在此範例中，最新的要求可以轉送為第13天。

## <a name="enable-requests"></a>啟用要求

1. 如果您想要讓存取套件立即提供給要求原則中的使用者要求，請按一下 **[是]** 啟用。

    在您完成建立存取套件之後，隨時都可以在未來啟用它。

    如果您選取 **[無] （僅限系統管理員直接指派）** ，而且您將 [啟用] 設定為 [**否**]，系統管理員將無法直接指派此存取套件。

    ![存取套件-原則-啟用原則設定](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. 按一下 [下一步]。
