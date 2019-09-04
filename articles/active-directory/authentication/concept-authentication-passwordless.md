---
title: Azure Active Directory 無密碼登入 (預覽)
description: 無密碼使用 FIDO2 安全性金鑰或 Microsoft Authenticator 應用程式 (預覽) 登入 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc633780d8b816d8fc2e313bb1955a5719979efe
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240876"
---
# <a name="what-is-passwordless"></a>什麼是無密碼？

多重要素驗證 (MFA) 是保護組織安全的絕佳方式, 但使用者在需要記住其密碼的同時, 也能讓您感到沮喪。 無密碼的驗證方法更方便, 因為密碼會被移除, 並以您所擁有的東西和您知道的東西取代。

|   | 您擁有的內容 | 您或知道的東西 |
| --- | --- | --- |
| 無密碼 | 電話或安全性金鑰 | 生物識別或 PIN |

在驗證方面, 每個組織都有不同的需求。 Microsoft 目前為我們的 Windows 電腦提供 Windows Hello。 我們會將 Microsoft Authenticator 應用程式和 FIDO2 安全性金鑰新增至無密碼系列。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

允許員工的電話成為無密碼的驗證方法。 除了密碼以外, 您可能已經使用 Microsoft Authenticator 應用程式作為便利的多重要素驗證選項。 但現在它是以無密碼選項的形式提供。

![使用 Microsoft Authenticator 應用程式登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

它藉由讓使用者能夠登入任何平臺或瀏覽器, 將任何 iOS 或 Android 手機轉換成強式的無密碼認證, 方法是藉由取得電話的通知、比對螢幕上顯示的數位與電話上的號碼, 然後使用其生物識別 (觸控或臉部), 或釘選以確認。

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO2 安全性金鑰是一種以 unphishable 標準為基礎的無密碼驗證方法, 可採用任何形式的規格。 快速身分識別線上 (FIDO) 是無密碼 authentication 的開放標準。 它可讓使用者和組織利用外部安全性金鑰或裝置內建的平臺金鑰, 使用標準來登入其資源, 而不需要使用者名稱或密碼。

對於公開預覽, 員工可以使用外部安全性金鑰來登入其 Azure Active Directory 聯結的 Windows 10 電腦 (執行1809版或更高版本), 並取得單一登入其雲端資源。 他們也可以登入支援的瀏覽器。

![使用安全性金鑰登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

雖然 FIDO 聯盟有許多 FIDO2 認證的金鑰, 但 Microsoft 還是需要由廠商實行一些選擇性的 FIDO2 CTAP 規格延伸模組, 以確保最高的安全性和最佳體驗。

安全性金鑰**必須**將下列功能和延伸模組從 FIDO2 CTAP 通訊協定實作為 Microsoft 相容:

| # | 功能/延伸模組信任 | 為什麼需要這項功能或延伸模組？ |
| --- | --- | --- |
| 1 | 常駐金鑰 | 這項功能可讓安全性金鑰成為可移植的, 您的認證會儲存在安全性金鑰上。 |
| 2 | 用戶端 pin | 這項功能可讓您使用第二個因素來保護您的認證, 並套用至沒有使用者介面的安全性金鑰。 |
| 3 | hmac-secret | 此延伸模組可確保您可以在裝置離線或在飛機模式時, 登入您的裝置。 |
| 4 | 每個 RP 的多個帳戶 | 這項功能可確保您可以在多個服務 (例如 Microsoft 帳戶和 Azure Active Directory) 上使用相同的安全性金鑰。 |

下列提供者提供 FIDO2 的安全性金鑰, 這些是已知與 paswordless 體驗相容的不同外型規格。 Microsoft 鼓勵客戶透過聯繫廠商以及 FIDO 聯盟, 來評估這些金鑰的安全性屬性。

| 提供者 | 連絡人 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

如果您是廠商, 而且想要在此清單上取得您的裝置[Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com), 請聯絡。

對於安全性敏感的企業, 或有不願意或無法使用其電話作為第二個因素的案例, FIDO2 安全性金鑰是很好的選擇。

## <a name="what-scenarios-work-with-the-preview"></a>哪些案例適用于預覽版？

- 系統管理員可以為其租使用者啟用無密碼 authentication 方法
- 系統管理員可以將所有使用者設為目標, 或選取其租使用者中每個方法的使用者/群組
- 使用者可以在其帳戶入口網站中註冊和管理這些無密碼驗證方法
- 終端使用者可以使用這些無密碼的驗證方法登入
   - Microsoft Authenticator 應用程式:適用于使用 Azure AD 驗證的案例, 包括跨所有瀏覽器、在 Windows 10 全新 (OOBE) 安裝期間, 以及任何作業系統上的整合式行動應用程式。
   - 安全性金鑰:適用于 Windows 10 1809 版或更高版本的鎖定畫面, 以及支援的瀏覽器 (例如 Microsoft Edge) 中的 web。

## <a name="next-steps"></a>後續步驟

[在您的組織中啟用 FIDO2 安全性金鑰 passwordlesss 選項](howto-authentication-passwordless-security-key.md)

[在您的組織中啟用以電話為基礎的無密碼選項](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部連結

[FIDO 聯盟](https://fidoalliance.org/)

[FIDO2 CTAP 規格](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
