---
title: Azure Active Directory Identity Protection 風險偵測參考 |Microsoft Docs
description: Azure Active Directory Identity Protection 風險偵測參考。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1005ac1853568222dea83a0f12293945825cf6d
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127583"
---
# <a name="azure-active-directory-identity-protection-risk-detections-reference"></a>Azure Active Directory Identity Protection 風險偵測參考

大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 探索遭入侵的身分識別並不容易。 Azure Active Directory 使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 每個偵測到的可疑動作都會儲存在名為風險偵測的記錄中。

## <a name="anonymous-ip-address"></a>匿名 IP 位址

**偵測類型：** 即時  
**舊名稱：** 從匿名 IP 位址登入

此風險偵測類型表示從匿名 IP 位址登入 (例如 Tor 瀏覽器、anonymizer Vpn)。
這些 IP 位址通常由意圖隱藏其登入遙測資料 (IP 位址、位置、裝置等) 的執行者所使用，以遂行其潛在惡意目的。

## <a name="atypical-travel"></a>非慣用登入位置

**偵測類型：** 離線  
**舊名稱：** 不可能進入非慣用位置

此風險偵測類型會識別源自地理位置相距較遠的兩個登入, 其中至少有一個位置對於使用者而言可能也是不尋常的, 但會提供過去的行為。 在許多其他因素中，此機器學習演算法會考量兩次登入之間的時間，以及使用者從第一個位置移到第二個位置所需的時間，這表示有不同的使用者正在使用相同的認證。

演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。 系統有前 14 天或 10 次登入的初始學習期間，它會在這段期間了解新使用者的登入行為。

## <a name="leaked-credentials"></a>認證外洩

**偵測類型：** 離線  
**舊名稱：** 認證外洩的使用者

此風險偵測類型指出使用者的有效認證已洩漏。
當網路罪犯入侵合法使用者的有效密碼時，罪犯通常會共用這些認證。 其做法通常是將認證公開張貼在暗網或分享網站上，或是在黑市上交易或銷售認證。 Microsoft 的認證外洩服務取得使用者名稱 / 密碼組的方式，是監視公用及深層網路，以及使用：

- 研究員
- 執法機關
- Microsoft 安全性小組
- 其他受信任的來源

服務從暗網、分享網站或前述來源取得使用者認證時，將會根據 Azure AD 使用者目前的有效認證對其進行檢查，以尋找有效的相符項目。

## <a name="malware-linked-ip-address"></a>已連結惡意程式碼的 IP 位址

**偵測類型：** 離線  
**舊名稱：** 從受感染的裝置登入

此風險偵測類型指出來自受惡意程式碼感染的 IP 位址的登入, 而這項功能已知會主動與 bot 伺服器通訊。 讓使用者裝置的 IP 位址與在 Bot 伺服器作用期間聯繫 Bot 伺服器的 IP 位址相互關聯，即可判定此類型。

## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登入屬性

**偵測類型：** 即時  
**舊名稱：** 從不熟悉的位置登入

此風險偵測類型會考慮過去的登入歷程記錄 (IP、緯度/經度和 ASN), 以尋找異常的登入。系統會儲存有關使用者先前所用位置的資訊，並考量這些「熟悉的」位置。 當登入來自不在熟悉位置清單中的位置時, 就會觸發風險偵測。 新建立的使用者將處於「學習模式」一段時間, 其中不熟悉的登入屬性風險偵測會在我們的演算法學慣用戶行為時關閉。 學習模式持續時間是動態的, 而且取決於演算法收集足夠的使用者登入模式資訊所需的時間。 最短持續時間為五天。 使用者可以在長時間沒有活動後回到學習模式。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 

我們也會針對基本驗證 (或舊版通訊協定) 執行這項偵測。 由於這些通訊協定沒有用戶端識別碼之類的新式屬性，因此只能以有限的遙測資料減少誤判。 我們建議客戶移轉至新式驗證。

## <a name="azure-ad-threat-intelligence"></a>Azure AD 威脅情報

**偵測類型：** 離線 <br>
**舊名稱：** 這項偵測會顯示在舊版的 Azure AD Identity Protection 報表中 (已標示為有風險的使用者、風險偵測) 為「有洩漏認證的使用者」

此風險偵測類型指出指定使用者不尋常的使用者活動, 或根據 Microsoft 內部和外部威脅情報來源, 與已知的攻擊模式一致。

## <a name="admin-confirmed-user-compromised"></a>系統管理員已確認使用者遭盜用

**偵測類型：** 離線 <br>
此偵測表示系統管理員已在具風險的使用者 UI 或使用 riskyUsers API 中, 選取 [確認使用者遭盜用]。 若要查看哪些系統管理員已確認此使用者遭入侵, 請檢查使用者的風險歷程記錄 (透過 UI 或 API)。

## <a name="malicious-ip-address"></a>惡意 IP 位址

**偵測類型：** 離線 <br>
此偵測會指出從惡意 IP 位址登入。 根據下列各項, IP 位址會視為惡意:
-   高失敗率 (因為從 IP 位址收到不正確認證)
-   其他 IP 信譽來源

## <a name="additional-risk-detected"></a>已偵測到其他風險

**偵測類型：** 即時或離線 <br>
這項偵測會指出偵測到上述其中一個 premium 偵測。 由於高階偵測僅適用于 Azure AD Premium P2 客戶, 因此其標題為「偵測到額外的風險」 (針對非 P2 客戶)。
