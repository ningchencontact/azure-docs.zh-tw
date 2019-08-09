---
title: 自助式密碼重設部署計畫-Azure Active Directory
description: 成功執行 Azure AD 自助式密碼重設的策略
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
ms.openlocfilehash: 7033c7bd3e783157280709b2c7e889473166ac84
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879219"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>部署 Azure AD 自助式密碼重設

自助式密碼重設 (SSPR) 是一項 Azure Active Directory 的功能, 可讓員工重設其密碼, 而不需要洽詢 IT 人員。 員工必須註冊或註冊自助式密碼重設, 才能使用服務。 在註冊期間, 員工會選擇其組織所啟用的一或多個驗證方法。

SSPR 可讓員工無論在何處或當天的時間, 都能快速地進行解除封鎖並繼續工作。 藉由允許使用者解除封鎖自己, 您的組織可以減少非生產力的時間, 以及最常見的密碼相關問題的高支援成本。

藉由將 SSPR 與組織中的另一個應用程式或服務一起部署, 協助使用者快速註冊。 此動作會產生大量的登入, 並將會驅動註冊。

在部署 SSPR 之前, 組織可能會想要判斷一段時間內發生多少密碼重設相關的服務台呼叫, 以及每次呼叫的平均成本。 他們可以使用此資料部署後, 顯示 SSPR 將會帶入您的組織的值。  

## <a name="how-sspr-works"></a>SSPR 的運作方式

1. 當使用者嘗試重設密碼時, 他們必須確認其先前註冊的驗證方法或方法, 以證明其身分識別。
1. 然後, 使用者輸入新密碼。
   1. 對於僅限雲端的使用者, 新的密碼會儲存在 Azure Active Directory 中。 如需詳細資訊, 請參閱[SSPR 的運作方式](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work)一文。
   1. 針對混合式使用者, 密碼會透過 Azure AD Connect 服務寫回內部部署 Active Directory。 如需詳細資訊, 請參閱[什麼是密碼回寫](concept-sspr-writeback.md#how-password-writeback-works)一文。

## <a name="licensing-considerations"></a>授權考慮

Azure Active Directory 是每位使用者授權的意義, 每位使用者都必須擁有其所使用之功能的適當授權。

如需有關授權的詳細資訊, 請參閱[Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>啟用 SSPR 和 MFA 的合併註冊

Microsoft 建議組織啟用 SSPR 和多重要素驗證的結合註冊體驗。 當您啟用此結合的註冊體驗時, 使用者只需要選取其註冊資訊一次, 即可啟用這兩項功能。

![結合的安全性資訊註冊](./media/howto-sspr-deployment/combined-security-info.png)

合併的註冊體驗不需要組織同時啟用 SSPR 和 Azure 多重要素驗證。 相較于傳統的個別元件, 結合的註冊體驗可為組織提供更好的使用者體驗。 如需有關合併註冊和如何啟用的詳細資訊, 請參閱[結合安全性資訊註冊 (預覽)](concept-registration-mfa-sspr-combined.md)一文。

## <a name="plan-the-configuration"></a>規劃設定

若要啟用 SSPR 以及建議的值, 需要下列設定。

| 區域 | 設定 | 值 |
| --- | --- | --- |
| **SSPR 屬性** | 已啟用自助式密碼重設 | 針對生產環境**選取**的試驗/ **All**群組 |
| **驗證方法** | 註冊所需的驗證方法 | 重設所需的一律為1 |
|   | 重設所需的驗證方法 | 一或兩個 |
| **註冊** | 登入時要求使用者註冊 | 是 |
|   | 要求使用者重新確認其驗證資訊之前所等候的天數 | 90–180天 |
| **通知** | 通知使用者密碼重設 | 是 |
|   | 當其他系統管理員重設其密碼時通知所有系統管理員 | 是 |
| **自訂** | 自訂技術服務人員連結 | 是 |
|   | 自訂的技術服務人員電子郵件或 URL | 支援網站或電子郵件地址 |
| **內部部署整合** | 將密碼寫回內部部署 AD | 是 |
|   | 允許使用者在不重設密碼的情況下解除鎖定帳戶 | 是 |

### <a name="sspr-properties-recommendations"></a>SSPR 屬性建議

啟用自助式密碼重設時, 請選擇要在試驗期間使用的安全性群組。

當您想要更廣泛地啟動服務時, 建議使用 [全部] 選項, 為組織中的每個人強制執行 SSPR。 如果您無法將設定為 [全部], 請選取適當的 Azure AD 安全性群組或已同步處理至 Azure AD 的 AD 群組。

### <a name="authentication-methods"></a>驗證方法

設定必要的驗證方法, 以至少註冊一個超過重設所需的數目。 允許多個使用者在需要重設時提供彈性。

設定**必要的方法數, 以重設**為適合您組織的層級。 其中一個需要最少的摩擦, 而兩者可能會增加您的安全性狀態。

如需 SSPR 的驗證方法、預先定義的安全性問題, 以及如何建立自訂安全性問題的詳細資訊, 請參閱[什麼是驗證方法](concept-authentication-methods.md)。

### <a name="registration-settings"></a>註冊設定

將 [**要求使用者在登入時註冊**] 設定為 **[是]** 。 這項設定表示在登入時, 系統會強制使用者註冊, 以確保所有使用者都受到保護。

設定**要求使用者重新確認其驗證資訊**在**90**到**180**天之間的天數, 除非您的組織有較短時間範圍的業務需求。

### <a name="notifications-settings"></a>通知設定

設定 [**通知使用者密碼重設**] 和 [**當其他系統管理員將其密碼重設**為 **[是]** 時通知所有系統管理員]。 選取 **[是]** , 可確保使用者知道密碼是否已重設, 而且所有系統管理員都知道何時會變更密碼, 以提高安全性。 如果使用者或系統管理員收到這類通知, 而他們尚未起始變更, 他們可以立即回報潛在的安全性缺口。

### <a name="customization"></a>自訂

自訂**技術服務人員電子郵件或 URL** , 以確保遇到問題的使用者可以快速取得協助, 這是很重要的。 將此選項設定為您的使用者熟悉的一般技術支援電子郵件地址或網頁。

### <a name="on-premises-integration"></a>內部部署整合

如果您有混合式環境, 請確定已將 [將**密碼寫回至內部部署 AD** ] 設定為 **[是]** 。 另請將 [允許使用者解除鎖定帳戶但不重設密碼] 設定為 [是], 因為它會提供更大的彈性。

### <a name="changingresetting-passwords-of-administrators"></a>變更/重設系統管理員的密碼

系統管理員帳戶是具有較高許可權的特殊帳戶。 為了保護它們, 下列限制適用于變更系統管理員的密碼:

- 內部部署企業系統管理員或網域系統管理員無法透過 SSPR 重設其密碼。 他們只能在其內部部署環境中變更其密碼。 因此, 我們建議您不要將內部內部部署的 AD 系統管理員帳戶同步至 Azure AD。
- 系統管理員無法使用秘密問題 & 答案作為重設密碼的方法。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多個身分識別管理系統的環境

如果環境中有多個身分識別管理系統, 例如 Oracle AM、SiteMinder 或其他系統等內部部署身分識別管理員, 則寫入 Active Directory 的密碼可能需要使用類似的工具同步處理到其他系統。具有 Microsoft Identity Manager (MIM) 的密碼變更通知服務 (PCNS)。 若要尋找更複雜案例的相關資訊, 請參閱在[網域控制站上部署 MIM 密碼變更通知服務一](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)文。

## <a name="plan-deployment-and-support-for-sspr"></a>規劃 SSPR 的部署和支援

### <a name="engage-the-right-stakeholders"></a>參與適當的專案關係人

當技術專案失敗時, 通常是因為對影響、結果和責任的預期不相符所造成。 若要避免這些錯誤, 請確定您參與的是正確的專案關係人, 並記載專案關係人及其專案的輸入和責任, 以充分瞭解專案中的專案關係人角色。

### <a name="communications-plan"></a>通訊計畫

通訊對於任何新服務的成功非常重要。 與您的使用者主動通訊如何使用服務, 以及他們可以如何在沒有如預期般運作時取得協助。 請參閱[Microsoft 下載中心的自助式密碼重設首](https://www.microsoft.com/download/details.aspx?id=56768)度發行資料, 以瞭解如何規劃您的終端使用者通訊策略。

### <a name="testing-plan"></a>測試計劃

若要確保您的部署如預期般運作, 您應該規劃一組將用來驗證執行的測試案例。 下表包含一些實用的測試案例, 您可以用來根據您的原則記錄組織預期的結果。

| 商業案例 | 預期的結果 |
| --- | --- |
| SSPR 入口網站可從公司網路記憶體取 | 由您的組織決定 |
| SSPR 入口網站可從公司網路外部存取 | 由您的組織決定 |
| 當使用者未啟用密碼重設時, 從瀏覽器重設使用者密碼 | 使用者無法存取密碼重設流程 |
| 當使用者未註冊密碼重設時, 從瀏覽器重設使用者密碼 | 使用者無法存取密碼重設流程 |
| 強制執行密碼重設註冊時的使用者登入 | 系統會提示使用者註冊安全性資訊 |
| 使用者在密碼重設註冊完成時登入 | 系統不會提示使用者註冊安全性資訊 |
| 當使用者沒有授權時, 可以存取 SSPR 入口網站 | 可存取 |
| 在使用者註冊後, 從 Windows 10 AADJ 或 H + AADJ 裝置鎖定畫面重設使用者密碼 | 使用者可以重設密碼 |
| SSPR 註冊和使用方式資料可供系統管理員近乎即時地使用 | 可透過 audit 記錄取得 |

### <a name="support-plan"></a>支援方案

雖然 SSPR 通常不會造成使用者的問題, 但請務必讓支援人員準備好處理可能發生的問題。

雖然系統管理員可以透過 Azure AD 入口網站變更或重設使用者的密碼, 但最好是藉由自助支援程式來協助解決此問題。

在本檔的操作指南一節中, 建立支援案例和其可能原因的清單, 並建立解決方案的指南。

### <a name="auditing-and-reporting"></a>稽核與報告

部署之後，許多組織會想要知道如何或是否實際上正在使用自助式密碼重設 (SSPR)。 Azure Active Directory (Azure AD) 提供的報告功能可協助您使用預先建立的報告來回答問題。

註冊和密碼重設的審查記錄可供使用30天。 因此, 如果公司內部的安全性審核需要較長的保留期, 則需要將記錄匯出並取用至 SIEM 工具, 例如[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 ArcSight。

在資料表中 (如下所示), 記錄備份排程、系統和負責的合作物件。 您可能不需要個別的審核和報告備份, 但您應該要有個別的備份, 以便從問題中復原。

|   | 下載頻率 | 目標系統 | 負責方 |
| --- | --- | --- | --- |
| 審核備份 |   |   |   |
| 報表備份 |   |   |   |
| 嚴重損壞修復備份 |   |   |   |

## <a name="implementation"></a>實作

執行分為三個階段:

- 設定使用者和授權
- 設定註冊和自助的 Azure AD SSPR
- 設定密碼回寫的 Azure AD Connect

### <a name="communicate-the-change"></a>傳達變更

開始執行您在規劃階段中開發的通訊計畫。

### <a name="ensure-groups-are-created-and-populated"></a>確定已建立並填入群組

請參閱規劃密碼驗證方法一節, 並確認試驗或生產環境的群組可供使用, 而且所有適當的使用者都會新增至群組。

### <a name="apply-licenses"></a>套用授權

您要執行的群組必須獲派 Azure AD premium 授權。 您可以直接將授權指派給群組, 也可以使用現有的授權原則, 例如透過 PowerShell 或以群組為基礎的授權。

如需將授權指派給使用者群組的相關資訊, 請參閱在[Azure Active Directory 中依群組成員資格將授權指派給使用者](../users-groups-roles/licensing-groups-assign.md)一文。

### <a name="configure-sspr"></a>設定 SSPR

#### <a name="enable-groups-for-sspr"></a>啟用 SSPR 的群組

1. 使用系統管理員帳戶存取 Azure 入口網站。
1. 選取 [所有服務], 然後在 [篩選] 方塊中輸入 Azure Active Directory, 然後選取 [Azure Active Directory]。
1. 在 [Active Directory] 分頁上, 選取 [密碼重設]。
1. 在 [屬性] 窗格中, 選取 [已選取]。 如果您想要啟用所有使用者, 請選取 [全部]。
1. 在 [預設密碼重設原則] 分頁中, 輸入第一個群組的名稱, 選取它, 然後按一下畫面底部的 [選取], 然後選取畫面頂端的 [儲存]。
1. 針對每個群組重複此程式。

#### <a name="configure-the-authentication-methods"></a>設定驗證方法

請參閱本檔的規劃密碼驗證方法一節中的規劃。

1. 選取 [註冊] 下的 [要求使用者在登入時註冊], 選取 [是], 然後設定到期前的天數, 然後選取 [儲存]。
1. 選取 [通知], 並根據您的方案進行設定, 然後選取 [儲存]。
1. 選取 [自訂], 並根據您的方案進行設定, 然後選取 [儲存]。
1. 選取 [內部部署整合], 並根據您的方案進行設定, 然後選取 [儲存]。

### <a name="enable-sspr-in-windows"></a>在 Windows 中啟用 SSPR

執行 Azure AD 加入或混合式 Azure AD 加入之1803版或更新版本的 Windows 10 裝置, 可以在 Windows 登入畫面重設其密碼。 如需設定這項功能的資訊和步驟, 請參閱[從登入畫面 Azure AD 密碼重設](tutorial-sspr-windows.md)一文

### <a name="configure-password-writeback"></a>設定密碼回寫

為貴組織設定密碼回寫的步驟可在下列文章[中找到: 如何:設定密碼回](howto-sspr-writeback.md)寫。

## <a name="manage-sspr"></a>管理 SSPR

管理與自助式密碼重設相關之功能的必要角色。

| 商務角色/角色 | Azure AD 角色 (如有必要) |
| :---: | :---: |
| 層級1技術服務人員 | 密碼管理員 |
| 層級2技術服務人員 | 使用者系統管理員 |
| SSPR 系統管理員 | 全域管理員 |

### <a name="support-scenarios"></a>支援案例

若要讓支援小組成功, 您可以根據您從使用者收到的問題來建立常見問題。 下表包含常見的支援案例。

| 案例 | 描述 |
| --- | --- |
| 使用者未提供任何已註冊的驗證方法 | 使用者嘗試重設其密碼, 但沒有任何可供使用的驗證方法 (例如: 他們離開家裡的行動電話, 且無法存取電子郵件) |
| 使用者未在其辦公室或行動電話上收到文字或呼叫 | 使用者嘗試透過文字或呼叫來驗證其身分識別, 但未收到文字/呼叫。 |
| 使用者無法存取密碼重設入口網站 | 使用者想要重設其密碼, 但未啟用密碼重設功能, 因此無法存取頁面來更新密碼。 |
| 使用者無法設定新密碼 | 使用者在密碼重設流程期間完成驗證, 但無法設定新密碼。 |
| 使用者在 Windows 10 裝置上看不到 [重設密碼] 連結 | 使用者嘗試從 Windows 10 鎖定畫面重設密碼, 但裝置未加入 Azure AD, 或未啟用 Intune 裝置原則 |

您可能也會想要包含如下的資訊, 以進行其他疑難排解。

- 已啟用 SSPR 的群組。
- 已設定的驗證方法。
- 與公司網路上或相關的存取原則。
- 常見案例的疑難排解步驟。

您也可以參閱關於疑難排解自助式密碼重設的線上檔, 以瞭解最常見的 SSPR 案例的一般疑難排解步驟。

## <a name="next-steps"></a>後續步驟

- [請考慮執行 Azure AD 密碼保護](concept-password-ban-bad.md)

- [請考慮執行 Azure AD 智慧鎖定](howto-password-smart-lockout.md)
