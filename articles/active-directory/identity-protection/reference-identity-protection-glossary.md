---
title: Azure Active Directory Identity Protection 詞彙
description: Azure Active Directory Identity Protection 詞彙
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232351"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection 詞彙

### <a name="at-risk-user"></a>有風險 (使用者)
A user with one or more active risk detections. 

### <a name="atypical-sign-in-location"></a>非典型登入位置
從特定使用者、類似使用者或租用戶不常用的地理位置登入。

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
A security module of Azure Active Directory that provides a consolidated view into risk detections and potential vulnerabilities affecting an organization’s identities.

### <a name="conditional-access"></a>條件式存取
用來保護資源存取的原則。 Conditional Access rules are stored in the Azure Active Directory and are evaluated by Azure AD before granting access to the resource.  範例規則包括根據使用者位置、裝置健康狀態或使用者驗證方法來限制存取。

### <a name="credentials"></a>認證
包含識別碼以及用來取得存取本機和網路資源之識別證明的資訊。 認證範例包括使用者名稱和密碼、智慧卡和憑證。

### <a name="event"></a>活動
Azure Active Directory 中的活動記錄。

### <a name="false-positive-risk-detection"></a>False-positive (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection was investigated and was incorrectly flagged as a risk detection.

### <a name="identity"></a>身分識別
必須透過以密碼或憑證等準則為基礎的驗證作業來確認的個人或實體。

### <a name="identity-risk-detection"></a>Identity risk detection
Azure AD event that was flagged as anomalous by Identity Protection, and may indicate that an identity has been compromised.

### <a name="ignored-risk-detection"></a>Ignored (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection is closed without taking a remediation action.

### <a name="impossible-travel-from-atypical-locations"></a>不可能來自非典型位置的移動
A risk detection triggered when two sign-ins for the same user are detected, where at least one of them is from an atypical sign-in location, and where the time between the sign-ins is shorter than the minimum time it would take to physically travel between these locations.  

### <a name="investigation"></a>調查
The process of reviewing the activities, logs, and other relevant information related to a risk detection to decide whether remediation or mitigation steps are necessary, understand if and how the identity was compromised, and understand how the compromised identity was used.

### <a name="leaked-credentials"></a>認證外洩
A risk detection triggered when current user credentials (user name and password) are found posted publicly in the Dark   web by our researchers.

### <a name="mitigation"></a>緩和
此動作可限制或消除攻擊者利用遭到入侵的身分識別或裝置的能力，但不需將身分識別或裝置還原至安全的狀態。 A mitigation does not resolve previous risk detections associated with the identity or device.

### <a name="multi-factor-authentication"></a>多因素驗證
此種驗證方法需要兩個或更多驗證方法，其中可能包含使用者擁有的事物 (例如憑證)、使用者知道的事物 (例如使用者名稱、密碼或通關密語)、實體屬性 (例如指紋) 以及個人屬性 (例如個人簽章)。

### <a name="offline-detection"></a>離線偵測
針對已經發生的事件，偵測異常狀況及評估事件的風險 (例如事後的登入嘗試)。

### <a name="policy-condition"></a>原則條件
安全性原則的一部分，定義原則中包含或排除的實體 (群組、使用者、應用程式、裝置平台、裝置狀態、IP 範圍、用戶端類型)。

### <a name="policy-rule"></a>原則規則
The part of a security policy that describes the circumstances that would trigger the policy, and the actions taken when the policy is triggered.

### <a name="prevention"></a>預防
預防藉由不當使用疑似或已知遭到入侵的身分識別或裝置來傷害組織的動作。 A prevention action does not secure the device or identity, and does not resolve previous risk detections.

### <a name="privileged-user"></a>特殊權限 (使用者)
A user that at the time of a risk detection, had permanent or temporary admin permissions to one or more resources in Azure Active Directory, such as a Global Administrator, Billing Administrator, Service Administrator, User administrator, and Password Administrator. 

### <a name="real-time"></a>即時
請參閱即時偵測。

### <a name="real-time-detection"></a>即時偵測
在允許事件繼續進行之前，偵測異常狀況及評估事件的風險 (例如登入嘗試)。

### <a name="remediated-risk-detection"></a>Remediated (risk detection)
A risk detection status set automatically by Identity Protection, indicating that the risk detection was remediated using the standard remediation action for this type of risk detection. For example, when the user password is reset, many risk detections that indicate that the previous password was compromised are automatically remediated.

### <a name="remediation"></a>補救
用來保護先前疑似或已知遭到入侵的身分識別或裝置的動作。 A remediation action restores the identity or device to a safe state, and resolves previous risk detections associated with the identity or device.

### <a name="resolved-risk-detection"></a>Resolved (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the user took an appropriate remediation action outside Identity Protection, and that the risk detection should be considered closed.

### <a name="risk-detection-status"></a>Risk detection status
A property of a risk detection, indicating whether the event is active, and if closed, the reason for closing it.

### <a name="risk-detection-type"></a>風險偵測類型
A category for the risk detection, indicating the type of anomaly that caused the event to be considered risky.

### <a name="risk-level-risk-detection"></a>Risk level (risk detection)
An indication (High, Medium, or Low) of the severity of the risk detection to help Identity Protection users prioritize the actions they take to reduce the risk to their organization. 

### <a name="risk-level-sign-in"></a>風險層級 (登入)
指出特定登入 (其他人正嘗試使用使用者的身分識別) 的可能性 (高、中或低)。

### <a name="risk-level-user-compromise"></a>風險層級 (使用者入侵)
指出身分識別遭到入侵的可能性 (高、中或低)。

### <a name="risk-level-vulnerability"></a>風險層級 (弱點)
指出弱點的嚴重性 (高、中或低)，可協助 Identity Protection 使用者排定為了降低組織風險而採取之行動的優先順序。

### <a name="secure-identity"></a>安全 (身分識別)
採取補救動作 (例如變更密碼或機器重新安裝映像)，將可能遭到入侵的身分識別還原至未遭入侵的狀態。

### <a name="security-policy"></a>安全性原則
原則規則和條件的集合。 原則可以套用至各種實體，例如使用者、群組、應用程式、裝置、裝置平台、裝置狀態、IP 範圍和 Auth2.0 用戶端類型。 啟用原則後，就會在納入原則的實體被核發資源的權杖時進行評估。

### <a name="sign-in-v"></a>登入 (動詞)
在 Azure Active Directory 中驗證身分識別。

### <a name="sign-in-n"></a>登入 (名詞)
在 Azure Active Directory 中驗證身分識別的程序或動作，以及擷取這項作業的事件。

### <a name="sign-in-from-anonymous-ip-address"></a>從匿名 IP 位址登入
A risk detection triggered after a successful sign-in from IP address that has been identified as an anonymous proxy IP address.

### <a name="sign-in-from-infected-device"></a>從受感染的裝置登入
A risk detection triggered when a sign-in originates from an IP address, which is known to be used by one or more compromised devices, which are actively attempting to communicate with a bot server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入
A risk detection triggered after a successful sign-in from an IP address with a high number of failed login attempts across multiple user accounts over a short period of time.

### <a name="sign-in-from-unfamiliar-location"></a>從不熟悉的位置登入
A risk detection triggered when a user successfully signs in from a new location (IP, Latitude/Longitude, and ASN).

### <a name="sign-in-risk"></a>登入風險
請參閱風險層級 (登入)

### <a name="sign-in-risk-policy"></a>登入風險原則
A Conditional Access policy that evaluates the risk to a specific sign-in and applies mitigations based on predefined conditions and rules.

### <a name="user-compromise-risk"></a>使用者入侵風險
請參閱風險層級 (使用者入侵)

### <a name="user-risk"></a>使用者風險
請參閱風險層級 (使用者入侵)。

### <a name="user-risk-policy"></a>使用者風險原則
A Conditional Access policy that considers the sign-in and applies mitigations based on predefined conditions and rules.

### <a name="users-flagged-for-risk"></a>標示有風險的使用者
Users that have risk detections, which are either active or remediated

### <a name="vulnerability"></a>弱點
Azure Active Directory 中的組態或狀況，此組態或狀況會使目錄容易受到入侵或威脅的影響。

## <a name="see-also"></a>請參閱

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
