---
title: Azure Active Directory 無密碼登入 （預覽）
description: 無密碼登入 Azure AD 使用 FIDO2 安全性金鑰或 Microsoft Authenticator 應用程式 （預覽）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712081"
---
# <a name="what-is-passwordless"></a>什麼是無密碼？

Multi-factor authentication (MFA) 是適合用來保護您的組織，但使用者取得挫折與其他的圖層上需要記住其密碼。 無密碼的驗證方法是更方便，因為移除和取代您可能必須再加上您的項目或您知道密碼。

|   | 您有的東西 | 您或知道的項目 |
| --- | --- | --- |
| 無密碼 | 電話或安全性金鑰 | 生物識別技術或 PIN |

我們了解每個組織在進行驗證時，會有不同的需求。 Microsoft 目前會提供 Windows Hello，我們無密碼的頂級體驗，針對 Windows 電腦。 我們要新增新的認證無密碼的系列：Microsoft Authenticator 應用程式和 FIDO2 安全性金鑰。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

允許您員工的電話，變成一種無密碼驗證方法。 您可能已經使用 Microsoft Authenticator 應用程式作為便利的多重要素驗證 選項，除了密碼。 但現在，可用做為無密碼的選項。

它會變成任何 iOS 或 Android 手機的強式、 無密碼的認證可讓使用者登入任何平台或瀏覽器取得通知，他們的電話號碼、 比對在電話上一個畫面上顯示的數字，然後使用其生物特徵辨識 （觸控動作，或面對） 或 PIN 以確認。

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO2 安全性金鑰都可以有任何外型規格的 unphishable 標準為基礎的無密碼驗證方法。 快速 Identity Online (FIDO) 是一套開放標準的無密碼驗證。 它可讓使用者與組織能利用標準登入他們的資源，而不需要使用者名稱或使用外部的安全性金鑰或內建於裝置平台金鑰的密碼。

針對公開預覽版，員工可以使用外部的安全性金鑰來登入 Azure Active Directory 已加入 Windows 10 電腦 （1809年或更高版本，請執行版本），並取得單一登入其雲端資源。 它們也可以支援的瀏覽器來登入。

雖然有許多是 FIDO2 FIDO 結盟經過認證的金鑰，Microsoft 就會需要 FIDO2 CTAP 規格廠商，以確保最大的安全性和最佳的體驗所實作的一些選擇性擴充功能。

安全性金鑰**必須**實作下列功能和 FIDO2 CTAP 通訊協定，與 Microsoft 相容的 extensions:

| # | 功能 / 擴充信任 | 為什麼這被需要？ |
| --- | --- | --- |
| 1 | 內建的索引鍵 | 此功能可在您的認證會儲存在安全性金鑰具有可攜性、 安全性金鑰。 |
| 2 | 用戶端 pin | 這項功能可讓您保護您的認證，與第二個因素，並套用至沒有使用者介面的安全性金鑰。 |
| 3 | hmac-secret | 此擴充功能可確保您可以登入您的裝置離線或在飛航模式中時。 |
| 4 | 每個 RP 的多個帳戶 | 這項功能可確保您可以跨多個服務，例如 Microsoft 帳戶及 Azure Active Directory 中使用相同的安全性金鑰。 |

下列提供者提供已知相容 paswordless 體驗使用不同的外型規格的 FIDO2 安全性的金鑰。 Microsoft 鼓勵客戶連絡廠商以及 FIDO 結盟評估這些機碼的安全性內容。

| 提供者 | 連絡人 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| 隱藏 | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

如果您的廠商和想来讓您的裝置，在這份清單，請連絡[ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com)。

FIDO2 安全性金鑰是企業非常敏感的安全性或案例或不願意或無法使用電話做為第二個因素的員工有絕佳的選項。

## <a name="what-scenarios-work-with-the-preview"></a>哪些案例會使用預覽版？

1. 系統管理員可以為其租用戶啟用無密碼驗證方法
1. 系統管理員可以針對所有使用者，或選取使用者/群組內的每個方法其租用戶
1. 使用者可以註冊及管理其帳戶入口網站中這些無密碼的驗證方法
1. 使用者可以使用這些無密碼驗證方法來進行登入
   1. Microsoft Authenticator 應用程式：將在其中使用 Azure AD 驗證時，每個案例中的工作包括在所有瀏覽器，在 Windows 10 出方塊 (OOBE) 安裝程式，並使用整合在任何作業系統上的行動裝置應用程式。
   1. 安全性金鑰：能夠在鎖定畫面，適用於 Windows 10 版 1809年或更新版本和支援的瀏覽器，例如 Microsoft Edge 中的 web 上。

## <a name="next-steps"></a>後續步驟

[啟用您的組織中的無密碼選項](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>外部連結

[FIDO 結盟](https://fidoalliance.org/)

[FIDO2 CTAP 規格](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
