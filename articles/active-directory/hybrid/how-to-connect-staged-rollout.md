---
title: Azure AD Connect：雲端驗證-分段推出 |Microsoft Docs
description: 說明如何使用分段推出，從同盟驗證遷移至雲端驗證。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/28/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 254fa924ea2104e76a475c106f16f4672dd1eec1
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033312"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>雲端驗證：分段推出（公開預覽）

這項功能可讓您使用分段的方法，從同盟驗證遷移至雲端驗證。

離開同盟驗證會造成影響。 例如，如果您有下列任何一項：
    
-  內部部署 MFA server 
-  使用智慧卡進行驗證 
-  其他僅限同盟功能

在切換到雲端驗證之前，應該將這些功能納入考慮。  在嘗試這項功能之前，建議您先參閱我們的指南，以瞭解如何選擇正確的驗證方法。 如需詳細資訊，請參閱[此表格](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)。

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>必要條件

-   您有一個具有同盟網域的 Azure AD 租使用者。

-   您已決定移至密碼雜湊同步處理 + 無縫 SSO **（選項 A）** 或傳遞驗證 + 無縫 Sso **（選項 B）。** 雖然無縫 SSO 是選擇性的，但建議您啟用無縫 SSO，讓使用已加入網域之電腦的使用者可以從公司網路內部，達到無訊息的登入體驗。

-   您已針對要遷移至雲端驗證的使用者，設定您需要的所有適當的租使用者商標和條件式存取原則。

-   如果您打算使用 Azure 多重要素驗證，建議您使用[聚合式註冊來進行自助式密碼重設（SSPR）和 AZURE MFA](../authentication/concept-registration-mfa-sspr-combined.md) ，讓使用者能夠註冊其驗證方法一次。

-   若要使用這項功能，您必須是租使用者的全域管理員。

-   若要在特定的 AD 樹系上啟用無縫 SSO，您必須是網域系統管理員。

## <a name="supported-scenarios"></a>支援的案例

這些案例支援分段推出：

- 這項功能僅適用于使用 Azure AD Connect 布建到 Azure AD 的使用者，不適用於僅限雲端的使用者。

- 這項功能僅適用于瀏覽器和新式驗證用戶端上的使用者登入流量。 使用舊版驗證的應用程式或雲端服務將會回到同盟驗證流程。 （例如：已關閉新式驗證的 Exchange online，或不支援新式驗證的 Outlook 2010）。

## <a name="unsupported-scenarios"></a>不支援的案例

分段首度發行不支援這些案例：

- 某些應用程式會在驗證期間傳送 "domain\_提示" 查詢參數給 Azure AD。 這些流程將會繼續，而啟用分段推出的使用者會繼續使用同盟進行驗證。

<!-- -->

- 系統管理員可以使用安全性群組來推出雲端驗證。 （建議使用雲端安全性群組，以避免在使用內部部署 AD 安全性群組時的同步延遲）。

    - **每項功能最多**可以使用10個群組;亦即，針對每個密碼雜湊同步處理/傳遞驗證/無縫 SSO。

    - **不支援**嵌套的群組。 這也是公開預覽的範圍。

    - 分段首度發行**不支援**動態群組。

    - 群組內的 Contact 物件將會封鎖新增的群組表單。

- 從同盟到雲端驗證的最終轉換仍然需要使用 Azure AD Connect 或 PowerShell。 這項功能不會將網域從同盟切換到受控。

- 當您第一次新增用於分段推出的安全性群組時，其限制為200個使用者，以避免 UX 超時。在 UX 中新增群組之後，您可以視需要將更多使用者直接新增至群組。

## <a name="get-started-with-staged-rollout"></a>開始使用分段推出

如果您想要使用分段推出來測試密碼雜湊同步（PHS）登入，請完成下列預先工作，以啟用密碼雜湊同步階段首度發行。

## <a name="pre-work-for-password-hash-sync"></a>密碼雜湊同步處理的前置工作

1. 從 Azure AD Connect 中的 [ [選用功能](how-to-connect-install-custom.md#optional-features) ] 頁面啟用 [密碼雜湊同步處理]。 

   ![Azure Active Directory Connect 中 [選用功能] 頁面的螢幕擷取畫面](media/how-to-connect-staged-rollout/sr1.png)

1. 請確定已執行完整的密碼雜湊同步處理週期，讓所有使用者的密碼雜湊都已同步處理到 Azure AD。 您可以使用[這裡](tshoot-connect-password-hash-synchronization.md)的 PowerShell 診斷來檢查密碼雜湊同步處理的狀態。

   ![AADConnect 疑難排解記錄的螢幕擷取畫面](./media/how-to-connect-staged-rollout/sr2.png)

如果您想要使用分段推出來測試通過驗證（PTA）登入，請完成下列預先工作，以啟用 PTA 進行分段推出。

## <a name="pre-work-for-pass-through-authentication"></a>傳遞驗證的前置工作

1. 識別執行 Windows Server 2012 R2 或更新版本的伺服器，而您想要讓通過驗證代理程式執行（**請勿選擇 Azure AD Connect 伺服器**）。 請確定伺服器已加入網域，可以使用 Active Directory 驗證選取的使用者，並可與輸出埠/Url 上的 Azure AD 通訊（請參閱詳細的[必要條件](how-to-connect-sso-quick-start.md)）。

1. [下載](https://aka.ms/getauthagent) & 在伺服器上安裝 Microsoft Azure AD Connect 驗證代理程式。 

1. 若要啟用 [高可用性](how-to-connect-sso-quick-start.md)，請在其他伺服器上安裝額外的驗證代理程式。

1. 請確定您已適當地設定[智慧鎖定設定](../authentication/howto-password-smart-lockout.md)。 這是為了確保使用者的內部部署 Active Directory 帳戶不會遭到不良的執行者鎖定。

我們建議您啟用無縫 SSO，而不論您選取來進行分段推出的登入方法（PHS 或 PTA）。 請完成下列前置工作，以啟用無縫 SSO 以進行分段推出。

## <a name="pre-work-for-seamless-sso"></a>無縫 SSO 的前置工作

使用 PowerShell 啟用 AD 樹系上的無縫 SSO。 如果您有多個 AD 樹系，請為每個樹系個別啟用相同的。 只有針對已選取進行分段推出的使用者才會觸發無縫 SSO，而且不會影響您現有的同盟設定。

**步驟摘要**

1. 首先，登入 Azure AD Connect Server。

2. 流覽至% programfiles%\\Microsoft Azure Active Directory Connect 資料夾。

3. 使用此命令匯入無縫 SSO PowerShell 模組： `Import-Module .\\AzureADSSO.psd1`。

4. 以系統管理員身分執行 PowerShell。 在 PowerShell 中，呼叫 `New-AzureADSSOAuthenticationContext`。 此命令應該會提供對話方塊，您可以在其中輸入租使用者的全域管理員認證。

5. 呼叫 `Get-AzureADSSOStatus \| ConvertFrom-Json`。 此命令會提供已啟用這項功能的 AD 樹系列表（查看 \"網域\" 清單）。 根據預設，租使用者層級的設定為 false。

   > **範例：** 
   > Windows PowerShell 輸出的![範例](./media/how-to-connect-staged-rollout/sr3.png)

6. 呼叫 `\$creds = Get-Credential`。 出現提示時，輸入預定 Azure AD 樹系的網域系統管理員認證。

7. 呼叫 `Enable-AzureADSSOForest -OnPremCredentials \$creds`。 此命令會從內部部署網域控制站，針對無縫 SSO 所需的這個特定 Active Directory 樹系，建立 AZUREADSSOACC 電腦帳戶。

8. 無縫 SSO 要求 Url 必須位於內部網路區域中。 請參閱[無縫單一登入快速入門](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)，以使用群組原則來部署這些 URL。

9.  如需完整的逐步解說，您也可以下載適用于無縫 SSO 的[部署計畫](https://aka.ms/SeamlessSSODPDownload)。

## <a name="enable-staged-rollout"></a>啟用分段推出

使用下列步驟，將特定功能（傳遞驗證/密碼雜湊同步/無縫 SSO）推出至群組中的一組選取的使用者：

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>在您的租使用者上啟用特定功能的分段推出

您可以推出其中一個選項

-   密碼雜湊同步處理 + 無縫 SSO **（選項 A）**

-   傳遞驗證 + 無縫 SSO **（選項 B）**

-   密碼雜湊同步處理 + 傳遞驗證 + 無縫 SSO **-\>** ***不受支援***

完成下列步驟：

1. 使用下列 URL 登入 Azure AD 入口網站，以存取預覽 UX。

   > <https://aka.ms/stagedrolloutux>

2. 按一下 [啟用受控使用者登入的分段推出（預覽）]

   例如 *：* （**選項 B**）如果您想要啟用密碼雜湊同步處理和無縫 SSO，請將密碼雜湊同步處理和無縫單一登入功能滑動到 [**開啟**]，如下所示。

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. 將個別群組新增至功能，以啟用傳遞驗證和無縫單一登入。 請確定安全性群組一開始不超過200成員，以避免 UX 超時。

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >群組中的成員會自動啟用以進行分段推出。 分段推出不支援嵌套和動態群組。

## <a name="auditing"></a>稽核

我們已針對我們針對分段推出所執行的不同動作，啟用了 audit 事件。

- 當您啟用密碼雜湊同步處理/傳遞驗證/無縫 SSO 的分段推出時，就會發生 Audit 事件。

  >[!NOTE]
  >使用 StagedRollout 開啟 SeamlessSSO 時所**記錄的 Audit**事件。

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- 將群組新增至密碼雜湊同步處理/傳遞驗證/無縫 SSO 時的 Audit 事件。

  >[!NOTE]
  >將群組新增至密碼雜湊同步處理以進行分段推出時所記錄的 Audit 事件

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- 已針對分段推出啟用已新增至群組的使用者時的 Audit 事件

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>驗證

若要使用密碼雜湊同步處理或傳遞驗證（使用者名稱/密碼登入）來測試登入：

1. 流覽至外部網路的私人瀏覽器會話 <https://myapps.microsoft.com>，並輸入選取要分段推出之使用者帳戶的 UserPrincipalName （UPN）。

1. 如果使用者已針對分段推出進行設定，則不會將使用者重新導向至您的同盟登入頁面，而會改為要求您在 [Azure AD 租使用者品牌的登入] 頁面上登入。

1. 藉由使用 UserPrincipalName 進行篩選，確保登入成功出現在[Azure AD 登入活動報告](../reports-monitoring/concept-sign-ins.md)中。

測試無縫 SSO 的登入：

1. 流覽 <https://myapps.microsoft.com>至來自內部網路的私人瀏覽器會話，然後輸入選取要分段推出之使用者帳戶的 UserPrincipalName （UPN）。

1. 如果使用者的目標是要分段推出無縫 SSO，使用者會看到「正在嘗試將您登入 ...」在以無訊息模式登入之前的訊息。

1. 藉由篩選 UserPrincipalName，確保登入成功出現在[Azure AD 登入活動報告](../reports-monitoring/concept-sign-ins.md)中。

若要檢查同盟提供者上是否仍發生使用者登入：

以下是您可以使用[這些指示](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)，追蹤所選分段推出使用者在 AD FS 上仍然發生的使用者登入。 請查看廠商檔，以瞭解如何在協力廠商同盟提供者上檢查此資訊。

## <a name="roll-back"></a>復原

### <a name="remove-a-user-from-staged-rollout"></a>從分段推出移除使用者

1.  從群組中移除使用者，會停用使用者的分段推出。

2.  如果您想要停用分段推出功能，請將功能滑回 [**關閉**] 狀態，以關閉分段推出。


## <a name="frequently-asked-questions"></a>常見問題集

-   **問：客戶可以在生產環境中使用這項功能嗎？**

-   答：是，這項功能可以在您的生產環境租使用者中使用，但建議您先在測試租使用者中嘗試此功能。

-   **問：此功能是否可以用來維護永久的「共存」，其中有些使用者會使用同盟驗證和其他雲端驗證？**

-   答：否，這項功能的設計目的是要在階段從同盟轉換至雲端驗證，最後再轉換到雲端驗證。 我們不建議使用永久混合狀態，因為這可能會導致非預期的驗證流程。

-   **問：我們可以使用 PowerShell 來執行分段推出嗎？**

-   答：是的，請在這裡尋找使用 PowerShell 執行分段推出的檔。
