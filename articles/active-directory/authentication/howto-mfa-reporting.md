---
title: Azure mfa-Azure Active Directory 的存取和使用方式報表
description: 說明如何使用 Azure Multi-Factor Authentication 功能 - 報告。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988e8982b6f06fb1210330c5cafdb696892794fe
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235519"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的報告

Azure Multi-Factor Authentication 提供數個報告，可供您和貴組織透過 Azure 入口網站使用。 下表列出可用的報告：

| 報表 | 位置 | 描述 |
|:--- |:--- |:--- |
| 已封鎖的使用者歷程記錄 | Azure AD > MFA Server > 封鎖/解除封鎖使用者 | 顯示使用者封鎖或解除封鎖要求的歷程記錄。 |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供整體使用量、使用者摘要和使用者詳細資料的相關資訊；以及在指定的日期範圍期間所提交的詐騙警示歷程記錄。 |
| 內部部署元件的使用方式 | Azure AD > MFA Server > 活動報表 | 透過 NPS 擴充功能、ADFS 和 MFA 伺服器提供 MFA 整體使用量的相關資訊。 |
| 已略過的使用者歷程記錄 | Azure AD > MFA Server > 單次許可 | 提供針對使用者許可 Multi-Factor Authentication 之要求的歷程記錄。 |
| 伺服器狀態 | Azure AD > MFA Server > 伺服器狀態 | 顯示與帳戶相關聯之 Multi-Factor Authentication Server 的狀態。 |

## <a name="view-mfa-reports"></a>檢視 MFA 報告

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [Azure Active Directory]   > [MFA Server]  。
3. 選取您要檢視的報告。

   ![在 Azure 入口網站中的 MFA Server 伺服器狀態報告](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD 登入報告

透過 [Azure 入口網站](https://portal.azure.com)中的**登入活動報告**，您可以取得判斷環境執行狀況所需的資訊。

登入報告可為您提供受控應用程式使用方式和使用者登入活動的相關資訊，包括 Multi-Factor Authentication (MFA) 使用方式的相關資訊。 MFA 資料可讓您深入了解 MFA 如何在您的組織中運作。 它可讓您回答問題，例如：

- 是否以 MFA 挑戰登入？
- 使用者如何完成 MFA？
- 為何使用者無法完成 MFA？
- 有多少使用者經過 MFA 挑戰？
- 有多少使用者無法完成 MFA 挑戰？
- 使用者會遇到的常見 MFA 問題是什麼？

此資料可透過 [Azure 入口網站](https://portal.azure.com)和[報告 API](../reports-monitoring/concept-reporting-api.md) 取得。

![在 Azure 入口網站中的 azure AD 登入報告](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>登入報告結構

MFA 的登入活動報告可讓您存取下列資訊：

**需要 MFA：** 登入是否需要 MFA。 依據每個使用者的 MFA、條件式存取或其他原因，可能需要 MFA。 可能的值為 [是]  或 [否]  。

**MFA 結果：** MFA 是否已滿足或拒絕的詳細資訊：

- 如果已滿足 MFA，此資料行提供如何滿足 MFA 的詳細資訊。
   - Azure Multi-Factor Authentication
      - 在雲端中完成
      - 由於租用戶上設定的原則所以已過期
      - 註冊提示
      - 因為在權杖中宣告而滿足
      - 因為外部提供者提供的宣告而滿足
      - 因為強式驗證而滿足
      - 因為運用的流程是 Windows 訊息代理程式登入流程而略過
      - 因為應用程式密碼而略過
      - 因為位置而略過
      - 因為已註冊的裝置而略過
      - 因為已記住的裝置而略過
      - 已成功完成
   - 重新導向至外部提供者以進行多重要素驗證

- 如果已拒絕 MFA，此資料行會提供拒絕的原因。
   - Azure Multi-Factor Authentication 遭到拒絕；
      - 驗證進行中
      - 重複的驗證嘗試
      - 輸入太多次不正確的代碼
      - 無效的驗證
      - 無效的行動應用程式驗證碼
      - 設定錯誤
      - 撥打電話轉到語音信箱
      - 電話號碼的格式無效
      - 服務錯誤
      - 無法接通使用者的電話
      - 無法將行動應用程式通知傳送到裝置
      - 無法傳送行動應用程式通知
      - 使用者拒絕驗證
      - 使用者未回應行動應用程式通知
      - 使用者沒有任何已註冊的驗證方法
      - 使用者輸入不正確的代碼
      - 使用者輸入不正確的 PIN
      - 使用者未成功驗證即掛斷電話
      - 使用者遭到封鎖
      - 使用者從未輸入驗證碼
      - 找不到使用者
      - 驗證碼已使用過一次

**MFA 驗證方法：** 使用者用來完成 MFA 的驗證方法。 可能的值包括：

- 簡訊
- 行動應用程式通知
- 撥打電話 (驗證電話)
- 行動應用程式驗證碼
- 撥打電話 (辦公室電話)
- 撥打電話 (替代驗證電話)

**MFA 驗證詳細資料：** 電話號碼的清除版本，例如：+X XXXXXXXX64。

**條件式存取**：尋找會影響到登入嘗試的條件式存取原則相關資訊，包括：

- 原則名稱
- 授與控制
- 工作階段控制項
- 結果

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>報告使用者的 PowerShell 註冊 MFA

首先，請確定您已[MSOnline V1 PowerShell 模組](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)安裝。

識別已使用下列 Powershell 註冊 MFA 的使用者。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

識別尚未使用下列 Powershell 註冊 MFA 的使用者。

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>可能的結果，在 活動報告

下表可用來疑難排解使用下載的版本，[多重要素驗證的活動] 報表的多因素驗證。 它們不會直接在 Azure 入口網站中。

| 通話結果 | 描述 | 廣泛的描述 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | 輸入 PIN | 使用者輸入 PIN。  如果驗證成功則表示使用者輸入正確的 pin 碼。  如果驗證遭到拒絕，則表示使用者輸入不正確的 PIN，或使用者設定為標準模式。 |
| SUCCESS_NO_PIN | 只輸入 # | 如果使用者設為 PIN 模式且驗證遭到拒絕，這表示使用者未輸入 PIN，只輸入 #。  如果使用者設為標準模式且驗證成功，這表示使用者只輸入 # 處於標準模式中的正確的做法。 |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # 項目後未按 | 使用者未傳送任何 DTMF 數字，因為並未輸入 #。  除非輸入 # 表示已完成的項目，不會傳送其他數字。 |
|SUCCESS_NO_PIN_BUT_TIMEOUT | 無電話輸入-逾時 | 已接聽電話，但沒有回應。  這通常表示電話已轉到語音信箱。 |
| SUCCESS_PIN_EXPIRED | PIN 已到期，且不會變更 | 使用者的 PIN 已到期和系統提示他們所要變更它，但是 PIN 變更未順利完成。 |
| SUCCESS_USED_CACHE | 使用快取 | 而不需要 Multi-factor Authentication 通話的驗證成功，因為相同的使用者名稱的前一個成功驗證發生在設定的快取時間範圍內。 |
| SUCCESS_BYPASSED_AUTH | 已略過的驗證 | 驗證已成功使用針對使用者起始單次許可。  請參閱許可略過使用者歷程記錄報告如需詳細資訊。 |
| SUCCESS_USED_IP_BASED_CACHE | 使用以 IP 為主的快取 | 自上一個成功的驗證，針對相同的使用者名稱、 驗證類型、 應用程式名稱，而不需要 Multi-factor Authentication 通話驗證成功，並在設定的快取時間範圍內發生的 IP。 |
| SUCCESS_USED_APP_BASED_CACHE | 使用應用程式為基礎的快取 | 自上一個成功的驗證，相同的使用者名稱、 驗證類型和設定的快取時間範圍內的應用程式名稱，而不需要 Multi-factor Authentication 通話成功驗證。 |
| SUCCESS_INVALID_INPUT | 電話輸入無效 | 電話傳來的回應無效。  這可能來自傳真機或數據機或使用者可能已進入 * 做為他們的 pin 碼的一部分。 |
| SUCCESS_USER_BLOCKED | 使用者遭到封鎖 | 封鎖使用者的電話號碼。  由使用者在驗證通話期間，或由系統管理員使用 Azure 入口網站，就會起始封鎖的號碼。 <br> 注意：  封鎖的號碼也是詐騙警示的副產品。 |
| SUCCESS_SMS_AUTHENTICATED | 已驗證簡訊 | 雙向簡訊使用者正確回覆使用其單次密碼 (OTP) 或 OTP + PIN。 |
| SUCCESS_SMS_SENT | 傳送簡訊 | 文字訊息已成功傳送包含單次密碼 (OTP) 的簡訊。  使用者會輸入 OTP 或 OTP + PIN，以完成驗證應用程式中。 |
| SUCCESS_PHONE_APP_AUTHENTICATED | 已驗證的行動裝置應用程式 | 透過行動裝置應用程式已成功驗證使用者。 |
| SUCCESS_OATH_CODE_PENDING | OATH 代碼擱置中 | 使用者最初 OATH 代碼，但並未回應。 |
| SUCCESS_OATH_CODE_VERIFIED | 已驗證 OATH 代碼 | 使用者輸入有效的 OATH 代碼出現提示時。 |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | 已驗證的遞補 OATH 代碼 | 使用者已拒絕使用其主要 Multi-factor Authentication 方法驗證，然後提供有效的 OATH 代碼，用於後援。 |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | 回答遞補安全性問題 | 使用者使用其主要 Multi-factor Authentication 方法驗證遭到拒絕，，然後回答正確進行遞補安全性問題。 |
| FAILED_PHONE_BUSY | 驗證進行中 | Multi-factor Authentication 已在處理此使用者的驗證。  這通常是相同的登入期間傳送多個驗證要求的 RADIUS 用戶端所造成。 |
| CONFIG_ISSUE | 無法接通電話 | 嘗試呼叫，但可能不是放置或無法回答。  這包括忙線訊號、 快速忙線訊號 （斷線），三-音調 （空號服務中不存在），在逾時鈴聲，依此類推。 |
| FAILED_INVALID_PHONENUMBER | 電話號碼格式無效 | 電話號碼的格式無效。  電話號碼必須是數值，且必須有 10 位數的國家/地區碼 + 1 （美國和加拿大）。 |
| FAILED_USER_HUNGUP_ON_US | 使用者已掛斷電話 | 使用者已接聽電，但未按下任何按鍵然後掛斷。 |
| FAILED_INVALID_EXTENSION | 分機號碼無效 | 擴充功能包含無效的字元。  只有數字、 逗號、 *，並允許 #。  @ 前置詞也可以使用。 |
| FAILED_FRAUD_CODE_ENTERED | 已輸入詐騙代碼 | 使用者已選擇回報詐騙，因而導致驗證遭到拒絕並封鎖的電話號碼的呼叫期間。| 
| FAILED_SERVER_ERROR | 無法撥打電話 | 多因素驗證服務無法撥打電話。 |
| FAILED_SMS_NOT_SENT | 無法傳送簡訊 | 無法傳送簡訊。  其驗證遭到拒絕。 |
| FAILED_SMS_OTP_INCORRECT | 簡訊 OTP 不正確 | 使用者輸入不正確的單次密碼 (OTP)，從他們收到的簡訊。  其驗證遭到拒絕。 |
| FAILED_SMS_OTP_PIN_INCORRECT | 簡訊 OTP + PIN 不正確 | 使用者輸入不正確的單次密碼 (OTP) 和 （或） 不正確的使用者 PIN。  其驗證遭到拒絕。 |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | 超過最大文字訊息 OTP 嘗試次數 | 使用者已超過單次密碼 (OTP) 嘗試次數上限。 |
| FAILED_PHONE_APP_DENIED | 拒絕行動裝置應用程式 | 拒絕行動裝置應用程式的驗證，使用者按下 [拒絕] 按鈕。 |
| FAILED_PHONE_APP_INVALID_PIN | 行動裝置應用程式 PIN 無效 | 在行動裝置應用程式中進行驗證時，使用者會輸入無效的 PIN。 |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | 行動裝置應用程式 PIN 未變更 | 使用者未成功完成必要的 PIN 變更中行動裝置應用程式。 |
| FAILED_FRAUD_REPORTED | 已回報詐騙 | 使用者回報詐騙行動裝置應用程式。 |
| FAILED_PHONE_APP_NO_RESPONSE | 行動裝置應用程式沒有回應 | 使用者未回應行動裝置應用程式驗證要求。 |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | 封鎖的行動應用程式的所有裝置 | 這位使用者行動裝置應用程式裝置不再回應通知，並已被封鎖。 |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | 行動裝置應用程式通知失敗 | 當您嘗試傳送通知給使用者的裝置上的行動應用程式時，就會發生失敗。 |
| FAILED_PHONE_APP_INVALID_RESULT | 行動裝置應用程式結果無效 | 行動裝置應用程式傳回無效的結果。 |
| FAILED_OATH_CODE_INCORRECT | OATH 代碼不正確 | 使用者輸入 OATH 代碼不正確。  其驗證遭到拒絕。 |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH 代碼 + PIN 不正確 | 使用者輸入 OATH 代碼不正確，及/或不正確的使用者 PIN。  其驗證遭到拒絕。 |
| FAILED_OATH_CODE_DUPLICATE | 重複的 OATH 代碼 | 使用者輸入 OATH 代碼，先前使用。  其驗證遭到拒絕。 |
| FAILED_OATH_CODE_OLD | OATH 代碼已過期 | 使用者輸入 OATH 代碼之前，先前使用 OATH 代碼。  其驗證遭到拒絕。 |
| FAILED_OATH_TOKEN_TIMEOUT | OATH 代碼結果逾時 | 使用者時間太長，無法輸入 OATH 代碼和多重要素驗證嘗試已經有已逾時。 |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | 安全性問題結果逾時 | 使用者時間太長，無法輸入安全性問題的答案和多重要素驗證嘗試已經有已逾時。 |
| FAILED_AUTH_RESULT_TIMEOUT | 驗證結果逾時 | 使用者花太多時間完成多重要素驗證嘗試。 |
| FAILED_AUTHENTICATION_THROTTLED | 驗證已節流處理 | 多重要素驗證嘗試已節流處理服務。 |

## <a name="next-steps"></a>後續步驟

* [適用於使用者](../user-help/multi-factor-authentication-end-user.md)
* [部署的位置](concept-mfa-whichversion.md)
