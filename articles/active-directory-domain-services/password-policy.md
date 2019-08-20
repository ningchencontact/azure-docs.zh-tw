---
title: 在 Azure AD Domain Services 中建立及使用密碼原則 |Microsoft Docs
description: 瞭解如何以及為何要使用更細緻的密碼原則來保護及控制 Azure AD DS 受控網域中的帳戶密碼。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617155"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>受控網域上的密碼和帳戶鎖定原則

若要管理 Azure Active Directory Domain Services (Azure AD DS) 中的帳戶安全性, 您可以定義更細緻的密碼原則來控制設定, 例如密碼長度下限、密碼到期時間或密碼複雜性。 預設密碼原則會套用至 Azure AD DS 受控網域中的所有使用者。 若要提供更細微的控制, 並符合特定的商務或合規性需求, 可以建立額外的原則, 並將其套用至特定的使用者群組。

本文說明如何使用 Active Directory 管理中心建立及設定更細緻的密碼原則。

## <a name="before-you-begin"></a>開始之前

若要完成本文, 您需要下列資源和許可權:

* 有效的 Azure 訂用帳戶。
  * 如果您沒有 Azure 訂用帳戶, 請[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租使用者, 可以與內部部署目錄或僅限雲端目錄同步處理。
  * 如有需要, 請[建立 Azure Active Directory 租][create-azure-ad-tenant]使用者, 或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租使用者中啟用並設定 Azure Active Directory Domain Services 受控網域。
  * 如有需要, 請完成教學課程, 以[建立及設定 Azure Active Directory Domain Services 實例][create-azure-ad-ds-instance]。
* 已加入 Azure AD DS 受控網域的 Windows Server 管理 VM。
  * 如有需要, 請完成教學課程以[建立管理 VM][tutorial-create-management-vm]。
* 屬於您 Azure AD 租使用者中*AZURE AD DC 系統管理員*群組成員的使用者帳戶。

## <a name="fine-grained-password-policies-fgpp-overview"></a>更細緻的密碼原則 (FGPP) 總覽

更細緻的密碼原則 (Fgpp) 可讓您將密碼和帳戶鎖定原則的特定限制套用到網域中的不同使用者。 例如, 若要保護特殊許可權帳戶, 您可以套用比一般非特殊許可權帳戶更嚴格的密碼設定。 您可以建立多個 Fgpp, 以指定 Azure AD DS 受控網域內的密碼原則。

您可以使用 FGPP 來設定下列密碼設定:

* 密碼長度下限
* 密碼歷程記錄
* 密碼必須符合複雜性需求
* 密碼最短使用期限
* 密碼最長使用期限
* 帳戶鎖定原則
  * 帳戶鎖定期間
  * 允許的失敗登入嘗試次數
  * 經過下列時間後重設失敗的登入嘗試計數

FGPP 只會影響在 Azure AD DS 中建立的使用者。 從 Azure AD 同步處理到 Azure AD DS 受控網域的雲端使用者和網域使用者不會受到密碼原則的影響。

原則會透過 Azure AD DS 受控網域中的群組關聯來散發, 而您所做的任何變更都會在下一次使用者登入時套用。 變更原則並不會解除鎖定已鎖定的使用者帳戶。

## <a name="default-fine-grained-password-policy-settings"></a>預設更細緻的密碼原則設定

在 Azure AD DS 受控網域中, 預設會設定下列密碼原則, 並套用至所有使用者:

* **最小密碼長度 (字元數):** 7
* **密碼最長使用期限 (存留期):** 90 天
* **密碼必須符合複雜性需求**

預設會設定下列帳戶鎖定原則:

* **帳戶鎖定持續時間:** 30
* **允許的失敗登入嘗試次數:** 5
* **重設失敗的登入嘗試次數:** 30 分鐘

使用這些預設設定, 如果在2分鐘內使用5個不正確密碼, 使用者帳戶就會被鎖定30分鐘。 30 分鐘後，帳戶會自動解除鎖定。

您無法修改或刪除預設內建的更細緻密碼原則。 相反地, *AAD DC 系統管理員*群組的成員可以建立自訂 FGPP, 並將它設定為覆寫 (優先于) 預設的內建 FGPP, 如下一節所示。

## <a name="create-a-custom-fine-grained-password-policy"></a>建立自訂的更細緻密碼原則

當您在 Azure 中建立和應用程式時, 您可能會想要設定自訂 FGPP。 需要建立自訂 FGPP 的一些範例包括設定不同的帳戶鎖定原則, 或為受控網域設定預設密碼存留期設定。

您可以建立自訂 FGPP, 並將它套用至 Azure AD DS 受控網域中的特定群組。 此設定會有效覆寫預設的 FGPP。 您也可以建立自訂的更細緻密碼原則, 並將其套用至您在 Azure AD DS 受控網域中建立的任何自訂 Ou。

若要建立更細緻的密碼原則, 您可以使用來自已加入網域之 VM 的 Active Directory 系統管理工具。 此 Active Directory 管理中心可讓您在 Azure AD DS 受控網域 (包括 Ou) 中, 查看、編輯和建立資源。

> [!NOTE]
> 若要在 Azure AD DS 受控網域中建立更細緻的密碼原則, 您必須登入屬於*AAD DC 系統管理員*群組成員的使用者帳戶。

1. 從 [開始] 畫面中, 選取 [系統**管理工具**]。 已安裝在教學課程中的可用管理工具清單, 以[建立管理 VM][tutorial-create-management-vm]。
1. 若要建立和管理 Ou, 請從系統管理工具清單中選取 [ **Active Directory 管理中心**]。
1. 在左窗格中, 選擇您的 Azure AD DS 受控網域, 例如*contoso.com*。
1. 在右側的 [工作] 面板中, 選取 [**新增] > [密碼設定**]。
1. 在 [**建立密碼設定**] 對話方塊中, 輸入原則的名稱, 例如*MyCustomFGPP*。 適當地將優先順序設定為覆寫預設的 FGPP (即*200*), 例如*1*。

    視需要編輯其他密碼原則設定, 例如 [**強制密碼歷程記錄**], 要求使用者建立與先前*24*個密碼不同的密碼。

    ![建立自訂的更細緻密碼原則](./media/how-to/custom-fgpp.png)

1. 取消核取 [**防止意外刪除**]。 如果選取此選項, 您就無法儲存 FGPP。
1. 在 [**直接套用至**] 區段中, 選取 [**新增**] 按鈕。 在 [選取使用者或群組] 對話方塊中，按一下 [位置] 按鈕。

    ![選取要套用密碼原則的使用者和群組](./media/how-to/fgpp-applies-to.png)

1. 更細緻的密碼原則只能套用至群組。 在 [**位置**] 對話方塊中, 展開功能變數名稱, 例如*contoso.com*, 然後選取 OU, 例如**AADDC Users**。 如果您有一個自訂 OU, 其中包含您想要套用的使用者群組, 請選取該 OU。

    ![選取群組所屬的 OU](./media/how-to/fgpp-container.png)

1. 輸入您想要套用原則的組名, 然後選取 [**檢查名稱**] 以驗證該群組是否存在。

    ![搜尋並選取要套用 FGPP 的群組](./media/how-to/fgpp-apply-group.png)

1. 在 [**直接套用至**] 區段中, 使用您所選取的組名, 選取 **[確定]** 儲存您的自訂密碼原則。

## <a name="next-steps"></a>後續步驟

如需更細緻的密碼原則及使用 Active Directory 系統管理中心的詳細資訊, 請參閱下列文章:

* [深入瞭解更細緻的密碼原則](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 系統管理中心設定更細緻的密碼原則](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
