---
title: 自助式密碼重設部署計劃-Azure Active Directory
description: 策略，成功實作 Azure AD 自助式密碼重設
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440960"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>部署 Azure AD 自助式密碼重設

自助式密碼重設 (SSPR) 是 Azure Active Directory 功能，讓員工能夠重設其密碼，而不需要連絡 IT 人員。 員工必須註冊，或針對自助式密碼重設之前先使用服務註冊。 在註冊期間，員工會選擇啟用其組織的一或多個驗證方法。

SSPR 讓員工能夠快速取得解除封鎖，並繼續運作，無論他們身在何處或一天的時間。 藉由允許使用者自行解除封鎖，非生產時間並提高支援成本，最常見的密碼相關問題，可以減少您的組織。

協助使用者取得快速與另一個應用程式或您組織中的服務一起部署 SSPR 註冊。 此動作將會產生大量的登入，並將磁碟機的註冊。

在部署之前 SSPR，組織可能想要判斷多少密碼重設相關的服務台電話發生時間和每個呼叫的平均成本。 他們可以使用此部署的資料後，若要顯示的值，SSPR，將帶到您的組織。  

## <a name="how-sspr-works"></a>SSPR 的運作方式

1. 當使用者嘗試重設密碼時，他們必須驗證其先前已註冊的驗證方法或方法，以證明其身分識別。
1. 然後使用者必須輸入新密碼。
   1. 僅限雲端使用者的新密碼會儲存在 Azure Active Directory。 如需詳細資訊，請參閱文章[如何 SSPR 的運作](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work)。
   1. 對於混合式使用者，密碼會回寫至內部部署 Active Directory，透過 Azure AD Connect 服務。 如需詳細資訊，請參閱文章[什麼是密碼回寫](concept-sspr-writeback.md#how-password-writeback-works)。

## <a name="licensing-considerations"></a>授權考量

Azure Active Directory 是每個使用者必須具有適當授權，它們都利用的功能授權每位使用者的意義。

- 自助式密碼重設為僅限雲端的使用者可與 Azure AD Basic 或更新版本。
- 自助式密碼重設內部部署回寫進行混合式環境，您需要 Azure AD Premium P1 或以上。

授權的詳細資訊可於[Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>啟用合併的註冊 SSPR 的 MFA

Microsoft 建議組織啟用 SSPR 和多重要素驗證的合併的註冊體驗。 當您啟用這項合併的註冊體驗時，使用者只需選取其註冊資訊一次啟用這兩項功能。

![合併的安全性資訊註冊](./media/howto-sspr-deployment/combined-security-info.png)

合併的註冊體驗不需要啟用 SSPR 和 Azure Multi-factor Authentication 使用的組織。 合併的註冊體驗提供較佳使用者體驗相較於傳統的個別元件的組織。 合併的註冊，以及如何啟用的詳細資訊可在發行項[結合安全性資訊註冊 （預覽）](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>規劃設定

下列設定，才能啟用 SSPR，以及建議值。

| 領域 | 設定 | 值 |
| --- | --- | --- |
| **SSPR 屬性** | 已啟用自助式密碼重設 | **選取**試驗群組 /**所有**用於生產環境 |
| **驗證方法** | 註冊所需的驗證方法 | 一律 1 超過所需的重設 |
|   | 重設所需的驗證方法 | 一或兩個 |
| **註冊** | 登入時要求使用者註冊 | 是 |
|   | 系統會要求使用者重新確認其驗證資訊之前的天數 | 90 – 180 天 |
| **通知** | 通知使用者密碼重設 | 是 |
|   | 當其他系統管理員重設其密碼時通知所有系統管理員 | 是 |
| **自訂** | 自訂技術服務人員連結 | 是 |
|   | 自訂技術支援中心電子郵件或 URL | 支援網站或電子郵件地址 |
| **內部部署整合** | 將密碼寫回內部部署 AD | 是 |
|   | 允許使用者解除鎖定帳戶但未重設密碼 | 是 |

### <a name="sspr-properties-recommendations"></a>SSPR 屬性建議

當啟用自助式密碼重設時，選擇要在試用期間使用的安全性群組。

當您規劃更廣泛地啟動服務時，我們建議使用所有選項來強制執行組織中的每個人的 SSPR。 如果您不能設定為 [所有]、 選取適當的 Azure AD 安全性群組或 AD 群組同步至 Azure AD。

### <a name="authentication-methods"></a>驗證方法

設定註冊至少一個重設所需的數目範圍內所需的驗證方法。 允許多個可讓使用者彈性在需要重設時。

設定**重設所需的方法數目**適合您組織的層級。 雖然兩個可能會增加您的安全性狀態，其中一個需要最低的摩擦。

請參閱[什麼是驗證方法](concept-authentication-methods.md)如的驗證方法可供 SSPR、 預先定義的安全性問題，以及如何建立自訂的安全性問題的詳細資訊。

### <a name="registration-settings"></a>註冊設定

設定**要求使用者在登入時註冊**要**是**。 這個設定表示，會強制使用者註冊時登入，確保所有使用者會受到都保護。

設定**之前要求使用者重新確認其驗證資訊的天數**來之間**90**並**180**天，除非您的組織有商務需求較短的時間範圍內。

### <a name="notifications-settings"></a>通知設定

設定這兩**通知使用者密碼重設**並**其他管理員重設其密碼時通知所有系統管理員**來**是**。 選取**是**兩者會增加安全性，以確保已重設其密碼，且所有系統管理員會感知時系統管理員變更密碼時，會了解使用者。 如果使用者或系統管理員會收到這類通知，而且它們無法起始變更，他們可以立即回報潛在的安全性缺口。

### <a name="customization"></a>自訂

請務必自訂**技術服務人員電子郵件或 URL**以確保發生問題的使用者可以快速取得協助。 常見的技術服務人員電子郵件地址或您使用者熟悉的網頁設定這個選項。

### <a name="on-premises-integration"></a>內部部署整合

如果您有混合式環境，請確認**將密碼寫回內部部署 AD**設為**是**。 也請設定 允許使用者解除鎖定帳戶但未重設為 是 的密碼，讓它們更大的彈性。

### <a name="changingresetting-passwords-of-administrators"></a>系統管理員的變更/重設密碼

系統管理員帳戶是以更高權限的特殊帳戶。 若要保護它們，下列限制適用於變更的系統管理員密碼：

- 在內部部署企業系統管理員 」 或 「 網域系統管理員無法重設其密碼透過 SSPR。 它們只能變更他們在其內部部署環境中的密碼。 因此，我們建議不同步處理內部部署 AD 系統管理員帳戶至 Azure AD。
- 系統管理員無法使用重設密碼，做為方法密碼問題與解答。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多個身分識別管理系統的環境

如果有多個身分識別管理系統，在內部部署身分識別管理員，如 Oracle AM 等這類的環境內，SiteMinder，或其他系統，然後寫入到 Active Directory 的密碼可能需要同步處理到其他系統使用之類的工具密碼變更通知服務 (PCNS) 使用 Microsoft Identity Manager (MIM)。 若要尋找此更複雜的案例的詳細資訊，請參閱文章[部署 MIM 密碼變更通知服務，網域控制站上](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)。

## <a name="plan-deployment-and-support-for-sspr"></a>規劃部署與支援，sspr

### <a name="engage-the-right-stakeholders"></a>進行正確的專案關係人

當技術專案失敗時，它們通常這樣因為不相符的期望上影響、 結果和責任。 若要避免這些陷阱，確定吸引人的權限的共同工作人員，專案中的專案關係人角色都能理解的專案關係人和專案的意見和權責加入註解。

### <a name="communications-plan"></a>溝通計劃

通訊非常重要的任何新的服務成功。 主動與您的使用者，如何使用服務，以及他們可以執行以取得說明，如果項目未如預期運作。 檢閱[自助式密碼重設 Microsoft download center 上的首度發行材料](https://www.microsoft.com/download/details.aspx?id=56768)以了解如何規劃您的使用者通訊策略。

### <a name="testing-plan"></a>測試計劃

若要確保您的部署如預期運作，您應該規劃出一組您用來驗證實作的測試案例。 下表包含一些有用的測試案例，您可以使用您的組織預期的結果，您的原則為基礎的文件。

| 商業案例 | 預期的結果 |
| --- | --- |
| SSPR 入口網站是可從公司網路存取 | 取決於您的組織 |
| SSPR 入口網站是可從公司網路外部存取 | 取決於您的組織 |
| 當使用者未啟用密碼重設時，重設使用者密碼從瀏覽器 | 使用者不能夠存取密碼重設流程 |
| 當使用者尚未註冊密碼重設時，請重設使用者密碼從瀏覽器 | 使用者不能夠存取密碼重設流程 |
| 會強制執行使用者登入密碼時重設註冊 | 系統會提示使用者註冊安全性資訊 |
| 使用者登入密碼重設註冊時完成 | 不會提示使用者註冊安全性資訊 |
| SSPR 入口網站時，可存取的使用者沒有授權 | 可存取 |
| 使用者已註冊之後，重設使用者密碼，從 Windows 10 AADJ 或 H + AADJ 裝置鎖定畫面 | 使用者可以重設密碼 |
| 近乎即時地處於系統管理員能夠使用 SSPR 註冊和使用方式資料 | 可透過稽核記錄檔 |

### <a name="support-plan"></a>支援計劃

雖然 SSPR 通常不會建立使用者問題，就一定要有的支援人員準備好應付可能發生的問題。

雖然系統管理員可以變更，或透過 Azure AD 入口網站的使用者重設密碼，最好是幫助您解決此問題，透過自助支援程序。

在這份文件操作指南區段中，建立一份支援案例，以及其可能的原因，並建立解決方案的指南。

### <a name="auditing-and-reporting"></a>稽核與報告

部署之後，許多組織會想要知道如何或是否實際上正在使用自助式密碼重設 (SSPR)。 Azure Active Directory (Azure AD) 提供的報告功能可協助您使用預先建立的報告來回答問題。

註冊和密碼重設稽核記錄可供使用 30 天。 因此，如果公司內的安全性稽核需要較長的保留期，記錄檔需要匯出並取用至 SIEM 工具這類[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、 Splunk、 或 ArcSight。

在資料表中，類似下面，文件的備份排程、 系統，以及負責的合作對象。 您可能不需要個別的稽核和報告的備份，但您應該有個別的備份，從中您可以從問題復原。

|   | 下載的頻率 | 目標系統 | 負責的對象 |
| --- | --- | --- | --- |
| 稽核備份 |   |   |   |
| 報表的備份 |   |   |   |
| 災害復原的備份 |   |   |   |

## <a name="implementation"></a>實作

實作可分為三個階段：

- 設定使用者與授權
- 設定 Azure AD SSPR 註冊和自助服務
- 設定 Azure AD Connect 進行密碼回寫

### <a name="communicate-the-change"></a>傳達變更

開始實作您在規劃階段開發溝通計劃。

### <a name="ensure-groups-are-created-and-populated"></a>請確定群組是建立並填入

參考的規劃密碼驗證方法 」 一節，並確認試用或生產實作的群組，而且所有適當的使用者會新增至群組。

### <a name="apply-licenses"></a>套用授權

您要實作的群組必須具有 Azure AD premium 授權指派給他們。 您可以授權直接指派給群組，或您可以使用這類現有的授權原則，透過 PowerShell 或以群組為基礎的授權。

在本文中，可將授權指派給使用者群組的相關資訊[將授權指派給 Azure Active Directory 中的群組成員資格使用者](../users-groups-roles/licensing-groups-assign.md)。

### <a name="configure-sspr"></a>設定 SSPR

#### <a name="enable-groups-for-sspr"></a>啟用 sspr 的群組

1. 存取 Azure 入口網站，以系統管理員帳戶。
1. 選取 所有服務，並在 篩選 方塊中，輸入 Azure Active Directory，，然後都選取 Azure Active Directory。
1. 在 [Active Directory] 刀鋒視窗中，選取 [密碼重設]。
1. 在 [屬性] 窗格中，選取 [選取]。 如果您想要所有使用者都啟用全選。
1. 在預設的密碼重設原則] 刀鋒視窗、 輸入第一個群組的名稱、 選取它，然後按一下底部的畫面上，選取和選取畫面頂端的 [儲存。
1. 每個群組重複此程序。

#### <a name="configure-the-authentication-methods"></a>設定的驗證方法

參考您打算從這份文件的規劃密碼驗證方法一節。

1. 選取註冊底下要求使用者在登入時註冊，選取 [是]，然後設定到期日之前的天數，然後選取 [儲存]。
1. 選取通知，並設定每個您的方案，然後選取 [儲存]。
1. 選取自訂項目，並依據您方案中，設定，然後選取 [儲存]。
1. 選取在內部部署環境整合，並依據您方案中，設定，然後選取 [儲存]。

### <a name="enable-sspr-in-windows"></a>啟用在 Windows 中的 SSPR

執行版本 1803年或更高版本的其中一個 Azure AD 已加入 Windows 10 裝置或加入混合式 Azure AD 可以重設其密碼，在 Windows 登入畫面。 資訊和步驟來設定這項功能可在發行項[從登入畫面重設 Azure AD 密碼](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>設定密碼回寫

若要設定密碼回寫，您的組織，請參閱本文的步驟[how to:設定密碼回寫](howto-sspr-writeback.md)。

## <a name="manage-sspr"></a>管理 SSPR

必要的角色來管理相關的自助式密碼重設功能。

| 商務角色/角色 | Azure AD 角色 （如有必要） |
| :---: | :---: |
| 層級 1 的技術支援中心 | 密碼管理員 |
| 層級 2 技術服務人員 | 使用者管理員 |
| SSPR 系統管理員 | 全域管理員 |

### <a name="support-scenarios"></a>支援的案例

若要啟用您的支援小組成功，您可以建立根據您收到來自您使用者的問題的常見問題集。 下表包含常見的支援案例。

| 案例 | 描述 |
| --- | --- |
| 使用者沒有任何可用的已註冊的驗證方法 | 使用者嘗試重設其密碼，但沒有任何驗證方法註冊可用 (範例： 它們在家裡保留其行動電話，且無法存取電子郵件) |
| 使用者未收到簡訊或在其辦公室或行動電話上呼叫 | 使用者嘗試驗證其身分識別，透過簡訊或通話，但未收到文字/呼叫。 |
| 使用者無法存取密碼重設入口網站 | 使用者會想要重設其密碼，但未啟用密碼重設，因此無法存取更新密碼頁面。 |
| 使用者無法設定新密碼 | 使用者完成密碼重設流程期間進行驗證，但無法設定新密碼。 |
| 使用者不會看到 Windows 10 裝置上重設密碼連結 | 使用者嘗試從 Windows 10 鎖定畫面上，重設密碼，但裝置可能是未加入 Azure AD 中，或未啟用 Intune 裝置原則 |

您也可以包含如下所示的其他疑難排解資訊。

- Sspr 已啟用的群組。
- 設定的驗證方法。
- 相關或公司網路的存取原則。
- 適用於常見案例的疑難排解步驟。

您也可以參考我們的線上文件，疑難排解自助式密碼重設，以了解最常見的 SSPR 案例的一般疑難排解步驟。

## <a name="next-steps"></a>後續步驟

- [請考慮實作 Azure AD 密碼保護](concept-password-ban-bad.md)

- [請考慮實作 Azure AD 智慧鎖定](howto-password-smart-lockout.md)
