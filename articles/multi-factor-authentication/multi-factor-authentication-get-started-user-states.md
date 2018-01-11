---
title: "Microsoft Azure Multi-Factor Authentication 使用者狀態"
description: "深入了解 Azure Multi-factor Authentication Server 中的使用者狀態。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: ad8d531d633eb65fe90404fdab0499b8e5332db6
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>如何要求使用者或群組使用雙步驟驗證

您可以採取兩種方法需要雙步驟驗證。 第一個選項是啟用每個使用者的 Azure Multi-factor Authentication (MFA)。 當個別啟用使用者時，他們執行雙步驟驗證每一次登入 (有一些例外，例如當登入時從受信任的 IP 位址或當_多因素驗證_開啟功能)。 第二個選項是設定條件式存取原則，以在某些情況下要求使用雙步驟驗證。

>[!TIP] 
>請選擇其中一種方法來要求使用雙步驟驗證，但不要兩種方法都使用。 啟用 Azure Multi-factor authentication 使用者，將會覆寫任何條件式存取原則。

## <a name="which-option-is-right-for-you"></a>哪些選項適合您？

**啟用 Azure Multi-factor Authentication Server 藉由變更使用者狀態**會要求兩步驟驗證的傳統方法。 這種方法適用於雲端 Azure MFA 和 Azure MFA Server。 每次登入時，您所啟用的所有使用者都執行雙步驟驗證。 啟用使用者，將會覆寫任何可能會影響該使用者的條件式存取原則。 

**使用條件式存取原則中啟用 Azure Multi-factor Authentication**是需要雙步驟驗證，更彈性的方法。 它只適用於 Azure MFA，在雲端中，不過，和_條件式存取_是[付費功能的 Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)。 您可以建立適用於群組以及個別使用者的條件式存取原則。 您可以對高風險群組施加比低風險群組更多的限制，或者，也可以只針對高風險雲端應用程式要求雙步驟驗證，至於低風險的雲端應用程式則略過雙步驟驗證。 

這兩個選項會提示使用者註冊 Azure 多重要素驗證之後需求開啟登入第一次。 這兩個選項也可設定與搭配[Azure Multi-factor Authentication 設定](multi-factor-authentication-whats-next.md)。

## <a name="enable-azure-mfa-by-changing-user-status"></a>藉由變更使用者狀態來啟用 Azure MFA

Azure Multi-Factor Authentication 中的使用者帳戶具有下列三種不同狀態：

| 狀態 | 說明 | 受影響的非瀏覽器應用程式 | 受影響的瀏覽器應用程式 | 受影響的新式驗證 |
|:---:|:---:|:---:|:--:|:--:|
| 已停用 |Azure MFA 未註冊的新使用者的的預設狀態。 |否 |否 |否 |
| 已啟用 |已在 Azure MFA 中註冊使用者，但使用者尚未註冊。 他們會收到註冊的下次登入時提示。 |編號  它們會繼續運作，直到註冊程序完成為止。 | 可以。 工作階段到期之後，Azure MFA 註冊是必要的。| 可以。 存取權杖到期之後，Azure MFA 註冊是必要的。 |
| 已強制 |已註冊使用者，而且使用者已完成 Azure MFA 的註冊程序。 |可以。  應用程式需要應用程式密碼。 |可以。 在登入時需要 azure MFA。 | 可以。 在登入時需要 azure MFA。 |

使用者的狀態會反映系統管理員是否已在 Azure MFA 中註冊他們，以及他們是否已完成註冊程序。

所有使用者一都開始*已停用*。 當您註冊 Azure MFA 的使用者時，其狀態會變更為*啟用*。 啟用的使用者登入並完成登錄程序，其狀態變更為*強制*。  

### <a name="view-the-status-for-a-user"></a>檢視使用者的狀態

使用下列步驟來存取您可在其中檢視並管理使用者狀態的頁面：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至 [Azure Active Directory] > [使用者和群組] > [所有使用者]。
3. 選取 [多重要素驗證]。
   ![選取 Multi-Factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. 顯示使用者狀態的新網頁隨即開啟。
   ![Multi-Factor Authentication 使用者狀態 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>變更使用者的狀態

1. 若要取得 Azure Multi-factor Authentication 使用上述步驟**使用者**頁面。
2. 尋找您想要為 Azure MFA 啟用的使用者。 您可能需要變更檢視頂端。 
   ![尋找使用者 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. 勾選其名稱旁的方塊。
4. 在右側，底下**快速步驟**，選擇**啟用**或**停用**。
   ![啟用所選的使用者 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >*啟用*使用者會自動切換成*強制*當他們註冊為 Azure MFA。 請勿手動變更的使用者狀態*強制*。 

5. 在開啟的快顯視窗中確認您的選取項目。 

啟用使用者之後，請透過電子郵件通知他們。 告訴他們系統會在他們下一次登入時要求其進行註冊。 此外，如果您的組織使用不支援新式驗證的非瀏覽器應用程式，它們需要建立應用程式密碼。 您也可以包含的連結[Azure MFA 使用者指南](./end-user/multi-factor-authentication-end-user.md)，幫助他們開始。

### <a name="use-powershell"></a>使用 PowerShell
若要變更所使用的使用者狀態[Azure AD PowerShell](/powershell/azure/overview)，變更`$st.State`。 有三個可能的狀態︰

* 已啟用
* 已強制
* 已停用  

請勿直接將使用者移至「已強制」狀態。 如果您這樣做，非瀏覽器型應用程式停止運作，因為使用者未經過 Azure MFA 註冊並取得[應用程式密碼](multi-factor-authentication-whats-next.md#app-passwords)。 

當您需要大量啟用使用者時，使用 PowerShell 是一個不錯的選項。 建立會在使用者清單中循環移動並啟用這些使用者的 PowerShell 指令碼：

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

下列指令碼為範例：

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>使用條件式存取原則來啟用 Azure MFA

_條件式存取_付費功能的 Azure Active Directory 中，有許多組態選項。 下列步驟會逐步解說一種用來建立原則的方式。 如需詳細資訊，請參閱 [Azure Active Directory 中的條件式存取](../active-directory/active-directory-conditional-access-azure-portal.md)。

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至 [Azure Active Directory] > [條件式存取]。
3. 選取 [新增原則]。
4. 在 [指派] 底下選取 [使用者和群組]。 使用**Include**和**排除**索引標籤，以指定哪些使用者和群組原則管理。
5. 在 [指派] 底下選取 [雲端應用程式]。 選擇要包含 [所有雲端應用程式]。
6. 在 [存取控制] 底下選取 [授與]。 選擇 [需要多重要素驗證]。
7. 開啟**啟用原則**至**上**，然後選取**儲存**。

條件式存取原則中的其他選項可讓您指定兩步驟驗證是必要的確切時間。 例如，您可以進行此類的原則： 當從沒有加入網域的裝置上不受信任的網路存取我們採購的應用程式嘗試約聘員工時，需要兩步驟驗證。 

## <a name="next-steps"></a>後續步驟

- 取得秘訣[條件式存取的最佳做法](../active-directory/active-directory-conditional-access-best-practices.md)。

- 管理 Azure Multi-factor Authentication 設定[您的使用者和他們的裝置](multi-factor-authentication-manage-users-and-devices.md)。
