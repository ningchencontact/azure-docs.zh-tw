---
title: Azure Active Directory Domain Services：密碼原則 | Microsoft Docs
description: 了解受控網域上的密碼原則
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 71511fd83f9c00f768f5f7bedb3516fef8599e70
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246835"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>受控網域上的密碼和帳戶鎖定原則
此文章說明受控網域上的預設密碼原則。 它也涵蓋如何設定這些原則。

## <a name="fine-grained-password-policies-fgpp"></a>更細緻的密碼原則 (FGPP)
您可以使用更細緻的密碼原則在單一網域內指定許多密碼原則。 FGPP 可讓您將不同的密碼和帳戶鎖定原則限制套用至網域中不同組的使用者。 例如，您可以將嚴格密碼設定套用至具權限的帳戶。

您可以使用 FGPP 來設定下列密碼設定：
* 密碼長度下限
* 密碼歷程記錄
* 密碼必須符合複雜性需求
* 密碼最短使用期限
* 密碼最長使用期限
* 帳戶鎖定原則
    * 帳戶鎖定期間
    * 允許的失敗登入嘗試次數
    * 經過下列時間後重設失敗的登入嘗試計數


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>受控網域上的預設更細緻密碼原則設定
以下螢幕擷取畫面說明 Azure AD Domain Services 受控網域上設定的預設更細緻密碼原則。

![預設的更細緻密碼原則](./media/how-to/default-fgpp.png)

> [!NOTE]
> 您無法修改或刪除預設的內建更細緻密碼原則。 'AAD DC Administrators' 群組的成員可以建立自訂 FGPP 並設定它來覆寫 (優先順序高於) 預設的內建 FGPP。
>
>

## <a name="password-policy-settings"></a>密碼原則設定
在受控網域上，預設會設定下列密碼原則：
* 最小密碼長度 (字元數)：7
* 密碼最長使用期限 (存留期)：90 天
* 密碼必須符合複雜性需求

### <a name="account-lockout-settings"></a>帳戶鎖定設定
在受控網域上，預設會設定下列帳戶鎖定原則：
* 帳戶鎖定期間：30
* 允許的失敗登入嘗試次數：5
* 經過下列時間後重設失敗的登入嘗試計數：30 分钟

實際上，如果在 2 分鐘內使用 5 個無效的密碼，使用者帳戶就會被鎖定 30 分鐘。 30 分鐘後，帳戶會自動解除鎖定。


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>在受控網域上建立自訂的更細緻密碼原則 (FGPP)
您可以建立自訂的 FGPP，並將其套用至受控網域中的特定群組。 此設定實際上會覆寫為受控網域設定的預設 FGPP。

> [!TIP]
> 只有 **'AAD DC Administrators'** 群組的成員具有建立自訂更細緻密碼原則的權限。
>
>

此外，您也可以建立自訂的更細緻密碼原則，並將其套用至您在受控網域上建立的任何自訂 OU。

您可以基於下列理由設定自訂 FGPP：
* 設定不同的帳戶鎖定原則。
* 設定受控網域的預設密碼存留期設定。

在受控網域上建立自訂的 FGPP：
1. 登入您用來管理受控網域的 Windows VM。 如果您沒有帳戶，請遵循指示[管理 Azure AD Domain Services 網域](manage-domain.md)。
2. 啟動 VM 上的 **Active Directory 管理中心**。
3. 按一下網域名稱 (例如 'contoso100.com')。
4. 按兩下 [系統]  以開啟 [系統] 容器。
5. 按兩下 [密碼設定容器]  。
6. 您會看到受控網域的預設內建 FGPP，名為 **AADDSSTFPSO**。 您無法修改這個內建的 FGPP。 不過，您可以建立新的自訂 FGPP 來覆寫預設 FGPP。
7. 在右側的 [工作]  面板上，按一下 [新增]  ，然後按一下 [密碼設定]  。
8. 在 [建立密碼設定]  對話方塊中，指定自訂密碼設定以套用成自訂 FGPP 的一部分。 請務必適當地設定優先順序以覆寫預設的 FGPP。

   ![建立自訂 FGPP](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **請務必從意外刪除選項中取消選取 [保護]。** 如果選取此選項，便無法儲存 FGPP。
   >
   >

9. 在 [直接套用至]  中，按一下 [新增]  按鈕。 在 [選取使用者或群組]  對話方塊中，按一下 [位置]  按鈕。

   ![選取使用者和群組](./media/how-to/fgpp-applies-to.png)

10. 在 [位置]  對話方塊中，展開網域名稱，然後按一下 [AADDC 使用者]  。 您現在可以從內建的使用者 OU 中選取要套用 FGPP 的群組。

    ![選取群組所屬的 OU](./media/how-to/fgpp-container.png)

11. 輸入群組的名稱，然後按一下 [檢查名稱]  按鈕以驗證群組是否存在。

    ![選取要套用 FGPP 的群組](./media/how-to/fgpp-apply-group.png)

12. 群組的名稱會顯示在 [直接套用至]  區段中。 按一下 [確定]  按鈕以儲存這些變更。

    ![套用 FGPP](./media/how-to/fgpp-applied.png)

> [!TIP]
> **針對自訂 OU 中的使用者帳戶套用自訂密碼原則：** 已微調的密碼原則只能套用到群組。 若要只針對來自自訂 OU 的使用者設定自訂密碼原則，請建立一個包含該 OU 中使用者的群組。
>
>

## <a name="next-steps"></a>後續步驟
* [了解 Active Directory 更細緻的密碼原則](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [使用 AD 系統管理中心來設定更細緻的密碼原則](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
