---
title: 主要重新整理權杖 (PRT) 與 Azure AD-Azure Active Directory
description: 角色是什麼及如何管理主要重新整理權杖 (PRT) Azure Active Directory 中？
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e195a93209875b9eabfaa2ad00772281922443c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476104"
---
# <a name="what-is-a-primary-refresh-token"></a>主要重新整理權杖是什麼？

主要重新整理權杖 (PRT) 是在 Windows 10、 iOS 和 Android 裝置上的 Azure AD 驗證的主要成品。 它是 JSON Web 權杖 (JWT) 特別發給 Microsoft 第一個合作對象權杖訊息代理程式來啟用單一登入 (SSO) 使用這些裝置上的應用程式間。 在本文中，我們將提供 PRT 如何發行、 使用以及 Windows 10 裝置上受保護的詳細資料。

本文假設您已了解不同的裝置有更多的狀態在 Azure AD 中，您可以使用和如何單一登入適用於 Windows 10。 在 Azure AD 中裝置的相關資訊，請參閱文章[什麼是 Azure Active Directory 中的裝置管理？](overview.md)

## <a name="key-terminology-and-components"></a>重要術語和元件

下列 Windows 元件扮演關鍵性的角色，在要求中和使用 PRT:

* **雲端驗證提供者**(CloudAP):CloudAP 會是新式驗證提供者的 Windows 登入，確認記錄到 Windows 10 裝置的使用者。 CloudAP 提供的外掛程式架構身分識別提供者可以建置啟用 Windows 驗證使用該身分識別提供者的認證。
* **Web 帳戶管理員**(WAM):WAM 是預設權杖訊息代理程式在 Windows 10 裝置上。 WAM 也提供的外掛程式架構身分識別提供者上建置，並在該身分識別提供者的信賴憑證者應用程式啟用 SSO。
* **Azure AD CloudAP 外掛程式**:CloudAP framework 所建置的 Azure AD 特定的外掛程式，來檢查 Windows 登入期間使用 Azure AD 的使用者認證。
* **Azure AD WAM 外掛程式**:WAM framework 所建置的 Azure AD 特定的外掛程式，可讓 SSO 都依賴 Azure AD 進行驗證的應用程式。
* **Dsreg**:在 Windows 10 中，處理所有的裝置狀態的裝置註冊程序是 Azure AD 特定的元件。
* **信賴平台模組**(TPM):TPM 是內建的裝置，提供給使用者和裝置的祕密硬體為基礎的安全性函式的硬體元件。 文章中，可以找到更多詳細資料[受信任的平台模組技術概觀](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview)。

## <a name="what-does-the-prt-contain"></a>PRT 包含哪些內容？

PRT 包含通常包含在任何 Azure AD 的重新整理權杖的宣告。 此外，還有 PRT 中包含某些裝置的特定宣告。 如下所示：

* **裝置識別碼**:PRT 發給特定裝置上的使用者。 裝置識別碼宣告`deviceID`決定 PRT 發出給使用者的裝置。 此宣告稍後發給 PRT 透過取得的權杖。 裝置識別碼宣告用來判斷裝置狀態或合規性為基礎的條件式存取的授權。
* **工作階段金鑰**:工作階段金鑰是加密的對稱金鑰，Azure AD 驗證服務，做為一部分 PRT 發出所產生。 PRT 來取得權杖的其他應用程式時，工作階段金鑰會做為所有權的證明。

### <a name="can-i-see-whats-in-a-prt"></a>我是否可以看到 PRT 功能？

PRT 是從任何用戶端元件不知道其內容的 Azure AD 傳送的不透明 blob。 您無法看到 PRT 的內容。

## <a name="how-is-a-prt-issued"></a>如何發行 PRT？

裝置註冊是裝置型驗證，Azure AD 中的必要條件。 PRT 發給使用者只能在已註冊的裝置上。 如需註冊裝置上的深入詳細資訊，請參閱文章[Windows Hello Business 」 和 「 裝置註冊](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)。 裝置註冊期間 dsreg 元件會產生兩組密碼編譯金鑰組：

* 裝置機碼 (dkpub/dkpriv)
* 傳輸索引鍵 (tkpub/tkpriv)

如果裝置具有有效且正常運作的 TPM，而公開金鑰會傳送至 Azure AD 裝置註冊程序期間，裝置的 tpm 會繫結的私用的索引鍵。 這些金鑰用來在 PRT 要求期間驗證裝置狀態。

在 Windows 10 裝置上兩個案例中的使用者驗證期間發出 PRT:

* **已加入 azure AD**或是**已加入混合式 Azure AD**:PRT 會發出 Windows 登入期間，當使用者登入其組織認證。 密碼和 Windows hello 企業版 PRT 比方說，發出所有支援的 Windows 10 的認證。 在此案例中，Azure AD CloudAP 外掛程式是 PRT 主要授權單位。
* **Azure AD 註冊裝置**:使用者會將第二個工作帳戶加入 Windows 10 裝置時，會發出 PRT。 使用者可以將帳戶加入 Windows 10 中兩個不同的方式-  
   * 新增透過帳戶**此裝置上隨處使用此帳戶**提示之後登入應用程式 (例如 Outlook)
   * 新增的帳戶**設定** > **帳戶** > **存取公司或學校** > **Connect**

在 Azure AD 註冊裝置的情況下，Azure AD WAM 外掛程式都是 PRT 主要授權單位，因為 Windows 登入不發生在與此 Azure AD 帳戶。

> [!NOTE]
> 第 3 方識別提供者必須支援 Ws-trust 通訊協定，若要啟用 Windows 10 裝置上的 PRT 發行。 沒有 1.1、WS-Trust、 PRT 無法發行給使用者混合式 Azure AD 已加入或 Azure AD 已加入裝置

## <a name="what-is-the-lifetime-of-a-prt"></a>什麼是 PRT 的存留期？

發行之後，PRT 有效期限為 14 天，並持續更新，只要使用者主動使用的裝置。  

## <a name="how-is-a-prt-used"></a>如何使用 PRT？

在 Windows 中的兩個關鍵元件使用 PRT:

* **Azure AD CloudAP 外掛程式**:在 Windows 登入，Azure AD CloudAP 外掛程式會使用使用者所提供的認證從 Azure AD 要求 PRT。 它也會啟用快取的登入當使用者沒有網際網路連線存取 PRT 快取。
* **Azure AD WAM 外掛程式**:當使用者嘗試存取應用程式時，Azure AD WAM 外掛程式會使用 PRT 来啟用 Windows 10 上的 SSO。 Azure AD WAM 外掛程式使用 PRT 要求依賴 WAM 權杖要求的應用程式的重新整理] 和 [存取權杖。 它也可讓 SSO 在瀏覽器上由 PRT 插入瀏覽器的要求。 瀏覽器中 Windows 10 的 SSO （原生） 支援 Microsoft Edge 和 Chrome （透過 Windows 10 帳戶 」 或 「 Office 線上擴充功能）。

## <a name="how-is-a-prt-renewed"></a>如何續訂 PRT？

PRT 續約兩個不同的方法中：

* **Azure AD CloudAP 外掛程式每隔 4 小時**:CloudAP 外掛程式更新 PRT Windows 登入期間每隔 4 小時。 如果使用者沒有網際網路連線，在這段期間，CloudAP 外掛程式將會更新 PRT 之後裝置連線到網際網路。
* **在應用程式的權杖要求期間的 azure AD WAM 外掛程式**:WAM 外掛程式啟用無訊息的應用程式的權杖要求，在 Windows 10 裝置上啟用 SSO。 WAM 外掛程式可以在兩個不同的方式，以更新這些權杖要求期間 PRT:
   * 應用程式要求 WAM 存取權杖以無訊息模式，但沒有任何重新整理權杖可供該應用程式。 在此情況下，WAM 用以 PRT 要求權杖的應用程式，並會在回應中的新 PRT 回來。
   * 應用程式要求存取權杖 WAM PRT 無效或 Azure AD 會要求額外的授權 (例如，Azure Multi-factor Authentication)。 在此案例中，WAM 初始互動式登入要求使用者重新驗證或提供額外的驗證並發出新 PRT 驗證成功。

### <a name="key-considerations"></a>主要考量

* PRT 只發出，並且在原生應用程式驗證期間更新。 PRT 未更新或瀏覽器工作階段期間發出。
* 在 Azure AD 中已加入和混合式 Azure AD 加入裝置，CloudAP 外掛程式是 PRT 主要授權單位。 如果 PRT 續約的 WAM 為基礎的權杖要求期間，PRT 傳送回 CloudAP 外掛程式，驗證與 Azure AD，才接受該值 PRT 的有效性。

## <a name="how-is-the-prt-protected"></a>如何保護 PRT？

PRT 受到繫結到裝置的使用者已登入。 Azure AD 和 Windows 10 啟用 PRT 保護透過下列方法：

* **在第一次登入期間**:在第一次登入時，會發出 PRT 簽署使用的密碼編譯在裝置註冊期間所產生的裝置金鑰的要求。 有效且正常運作的 TPM 的裝置，裝置金鑰受到由 TPM 防止任何惡意存取。 如果無法驗證對應的裝置金鑰簽章，不會發出 PRT。
* **在權杖要求和續約期間**:當發出 PRT 時，Azure AD 也會發出加密工作階段金鑰至裝置。 它會加密具有公用的傳輸索引鍵 (tkpub) 產生並傳送至 Azure AD 裝置註冊的一部分。 此工作階段金鑰只能由 TPM 所保護的私用的傳輸索引鍵 (tkpriv) 解密。 工作階段金鑰是傳送至 Azure AD 的任何要求的所有權證明 (POP) 索引鍵。  工作階段金鑰也受 TPM，且沒有其他作業系統元件可以存取它。 權杖要求或 PRT 更新要求安全地透過 TPM 此工作階段金鑰所簽署，因此，無法被竄改。 Azure AD 會導致無效的從裝置對應的工作階段金鑰的未簽署的任何要求。

藉由保護這些金鑰使用 TPM，惡意執行者無法取得索引鍵或其他地方重新執行攻擊者在沒有實際擁有裝置，TPM 是無法存取的 PRT。  因此，使用 TPM 可大幅增強的 Azure AD 已聯結，混合式 Azure AD 已加入，安全性，Azure AD 註冊裝置針對認證竊取。 基於效能和可靠性，TPM 2.0 是 Windows 10 上所有的 Azure AD 裝置註冊案例的建議的版本。

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>應用程式權杖和受保護的瀏覽器 cookie，方式為何？

**應用程式權杖**:當應用程式要求透過 WAM 的語彙基元時，Azure AD 會發出重新整理權杖和存取權杖。 不過，WAM 只傳回至應用程式的存取權杖，並保護其快取中的重新整理權杖並經過使用者的資料保護應用程式設計介面 (DPAPI) 金鑰的加密。 WAM 安全地使用重新整理權杖簽署要求的工作階段金鑰進一步發出存取權杖。 在本身的 Azure AD 中的 DPAPI 金鑰受到 Azure AD 為基礎的對稱金鑰。 當裝置需要使用 DPAPI 金鑰解密的使用者設定檔時，Azure AD 會提供加密工作階段金鑰，哪一個 CloudAP 外掛程式要求 TPM 解密的 DPAPI 金鑰。 這項功能可確保一致的保護 重新整理權杖，並避免應用程式實作自己的保護機制。  

**瀏覽器 cookie**:在 Windows 10 中，Azure AD 支援瀏覽器在 Internet Explorer 和 Microsoft Edge 中的 SSO 原生或 Google Chrome 中透過 Windows 10 帳戶延伸模組。 建置安全性的不只是用來保護 cookie，但也要傳送的 cookie 的端點。 瀏覽器 cookie 受到相同的方式是 PRT，藉由使用工作階段金鑰來簽署及保護 cookie。

當使用者啟動瀏覽器互動時，瀏覽器 （或延伸模組） 會叫用 COM 原生用戶端主機。 原生用戶端主機會確保頁面是來自其中一個允許的網域。 瀏覽器無法將原生用戶端的其他參數裝載，包括 nonce，不過原生用戶端主機保證的主機名稱的驗證。 原生用戶端主機會要求 PRT cookie 從 CloudAP 外掛程式，它會建立並使用 TPM 保護的工作階段金鑰簽署它。 PRT cookie 由工作階段金鑰簽署，因為它無法被竄改。 此 PRT cookie 會包含在 Azure AD 來驗證它來自於該裝置的要求標頭。 如果使用 Chrome 瀏覽器，在原生用戶端主機的資訊清單中明確定義的擴充功能可以叫用它提出這些要求時，防止任意的延伸模組。 Azure AD 驗證之後 PRT cookie，它會發出給瀏覽器工作階段 cookie。 此工作階段 cookie 也會包含與 PRT 發出相同的工作階段金鑰。 在後續的要求，有效地繫結至裝置的 cookie，並避免重新執行，從其他地方，會進行驗證的工作階段金鑰。

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT 何時取得之 MFA 宣告？

PRT 可以在特定情況下取得 multi-factor authentication (MFA) 宣告。 MFA 型 PRT 來要求應用程式的權杖，則會將 MFA 宣告傳輸至這些應用程式權杖。 這項功能為使用者提供順暢的體驗，藉由防止需要每個應用程式的 MFA 挑戰。 PRT 可以下列方式取得之 MFA 宣告：

* **使用 Windows Hello 登入商務**:Windows hello 企業版會取代密碼並使用密碼編譯金鑰來提供強大的雙因素驗證。 Windows hello 企業版是一個特定使用者在裝置上，並自行佈建需要 MFA。 當使用者登入 Windows hello 企業版中時，使用者的 PRT 取得之 MFA 宣告。 此案例也適用於使用者登入智慧卡，如果智慧卡驗證會產生從 ADFS MFA 宣告。
   * Windows hello 企業版會被視為多重要素驗證，MFA 宣告被更新重新整理 PRT 本身時，這樣當使用 WIndows hello 企業版的使用者登入時，會持續擴充 MFA 持續時間
* **WAM 互動式登入期間的 MFA**:在透過 WAM 權杖要求時，使用者是否需要進行 MFA，才能存取應用程式中，更新此類互動期間 PRT 是 imprinted 具有 MFA 宣告。
   * 在此情況下，MFA 宣告不會持續更新，因此 MFA 持續時間為基礎的目錄上設定的存留期。
* **裝置註冊期間的 MFA**:系統管理員已在 Azure AD 中設定其裝置設定[需要 MFA 才可註冊裝置](device-management-azure-portal.md#configure-device-settings)，使用者必須執行以完成註冊的 MFA。 在此過程中，會發給該使用者 PRT 會有在註冊期間取得的 MFA 宣告。 這項功能僅適用於執行聯結作業，不供其他使用者登入該裝置的使用者。
   * 類似於 WAM 互動式登入，MFA 宣告不會持續更新，因此 MFA 持續時間為基礎的目錄上設定的存留期。

Windows 10 維護 Prt 一個資料分割的清單，每個認證。 因此，會有每個 Windows Hello for Business、 密碼或智慧卡 PRT。 這項分割可確保 MFA 宣告隔離使用，且不混在權杖要求期間的認證為基礎。

## <a name="how-is-a-prt-invalidated"></a>如何失效 PRT？

PRT 無效在下列情況：

* **無效的使用者**:如果使用者已刪除，或在 Azure AD 中停用，其 PRT 就會失效，無法用來取得應用程式的權杖。 如果已刪除或停用的使用者已經登入至裝置之前，快取登入就其記錄中，直到 CloudAP 留意其無效的狀態。 一旦 CloudAP 判斷使用者無效，它會封鎖後續的登入。 無效的使用者自動封鎖登入沒有快取其認證的新裝置。
* **不正確的裝置**:如果裝置已刪除，或在 Azure AD 中停用，該裝置上取得 PRT 就會失效，無法用來取得權杖的其他應用程式。 如果使用者已登入不正確的裝置，他們可以繼續這麼做。 但在裝置上的所有權杖都會都失效，使用者不必 SSO 的任何資源從該裝置。
* **密碼變更**:使用者變更其密碼之後，使用先前的密碼取得 PRT 都被無效的 Azure AD。 在取得新 PRT 使用者的密碼變更結果。 此失效，可能會發生兩種不同方式：
   * 如果使用者登入 Windows 至新的密碼，CloudAP 捨棄舊 PRT，並要求 Azure AD 以發出新的 PRT 其新的密碼。 如果使用者沒有網際網路連線，新的密碼無法進行驗證，Windows 可能會需要使用者輸入舊密碼。
   * 如果使用者已使用舊密碼登入或變更其密碼之後登入 Windows 時，會將舊的 PRT 用於任何 WAM 為基礎的權杖要求。 在此案例中，系統會提示使用者重新驗證期間 WAM 權杖要求，並發出新的 PRT。
* **TPM 問題**:有時候，裝置的 TPM 可以折損或失敗，導致無法存取 TPM 所保護的金鑰。 在此情況下，裝置不具取得 PRT 或要求使用現有的 PRT，因為它不能證明持有的密碼編譯金鑰的權杖。 如此一來，任何現有的 PRT 都被無效的 Azure AD。 當 Windows 10 中，偵測到失敗時，它會起始重新註冊裝置，使用新的密碼編譯金鑰復原流程。 混合式 Azure Ad join，一樣的初始註冊中，復原會以無訊息模式不需要使用者輸入。 Azure AD 已加入或 Azure AD 註冊裝置時，復原必須由在裝置具有系統管理員權限的使用者身分執行。 在此案例中，會引導使用者已成功復原裝置的 Windows 提示用起始復原流程。

## <a name="detailed-flows"></a>詳細的流程

下圖說明中發出、 更新及使用 PRT 來要求存取權杖的應用程式的基礎詳細資料。 此外，這些步驟也會說明如何在這些互動期間套用上述的安全性機制。

### <a name="prt-issuance-during-first-sign-in"></a>在第一次登入期間 PRT 發行

![在第一次的登入的詳細流程期間 PRT 發行](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> 在 Azure AD 中已加入裝置，這項交換會以同步方式發出 PRT 之前使用者可以登入 Windows。 在混合式 Azure AD 已加入裝置，在內部部署 Active Directory 是主要的授權單位。 因此，使用者只能等到 PRT 發行以非同步方式發生時，它們可以取得登入，TGT。 此案例不適用於 Azure AD 註冊裝置，因為登入不會使用 Azure AD 認證。

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者輸入其密碼的登入 UI。 LogonUI 會將認證驗證緩衝區中傳遞至 LSA，進而將其內部傳遞給 CloudAP。 CloudAP 此將要求轉寄到 CloudAP 外掛程式。 |
| B | CloudAP 外掛程式初始領域探索要求以找出使用者的身分識別提供者。 如果使用者的租用戶同盟提供者安裝程式，Azure AD 傳回同盟提供者的中繼資料交換端點 (MEX) 端點。 如果不是，Azure AD 會傳回指出該使用者可以向 Azure AD 管理使用者。 |
| C | 如果使用者受管理，CloudAP 會從 Azure AD 取得的 nonce。 如果同盟使用者，CloudAP 外掛程式會要求 SAML 權杖，從同盟提供者，利用使用者的認證。 一旦它收到，SAML 權杖中，它會要求從 Azure AD 的 nonce。 |
| D | CloudAP 外掛程式建構使用者的認證、 nonce 以及訊息代理程式範圍的驗證要求，會要求簽署的裝置金鑰 (dkpriv)，並將它傳送至 Azure AD。 在同盟環境中，CloudAP 外掛程式會使用同盟提供者，而不是使用者所傳回的 SAML 語彙基元 ' 認證。 |
| E | Azure AD 會驗證使用者認證，nonce 及裝置簽章，確認裝置是有效的租用戶中並發出加密的 PRT。 PRT，以及 Azure AD 也會發出的對稱金鑰，稱為工作階段金鑰加密的 Azure AD 中使用的傳輸索引鍵 (tkpub)。 颾魤 ㄛ，工作階段金鑰也會嵌入 PRT。 此工作階段金鑰做為 PRT 的後續要求的所有權證明 (PoP) 索引鍵。 |
| F | CloudAP 外掛程式會將加密的 PRT 和工作階段金鑰傳遞至 CloudAP。 CloudAP 要求 TPM 使用的傳輸索引鍵 (tkpriv) 工作階段金鑰解密和重新加密使用 TPM 自己的金鑰。 CloudAP PRT 以及其快取中儲存加密的工作階段金鑰。 |

### <a name="prt-renewal-in-subsequent-logons"></a>在後續登入的 PRT 續約

![在後續登入的 PRT 續約](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者輸入其密碼的登入 UI。 LogonUI 會將認證驗證緩衝區中傳遞至 LSA，進而將其內部傳遞給 CloudAP。 CloudAP 此將要求轉寄到 CloudAP 外掛程式。 |
| B | 如果使用者先前已對使用者登入，Windows 會起始快取登入，並驗證認證進行登入的使用者。 每隔 4 小時，CloudAP 外掛程式會起始 PRT 更新以非同步的方式。 |
| C | CloudAP 外掛程式初始領域探索要求以找出使用者的身分識別提供者。 如果使用者的租用戶同盟提供者安裝程式，Azure AD 傳回同盟提供者的中繼資料交換端點 (MEX) 端點。 如果不是，Azure AD 會傳回指出該使用者可以向 Azure AD 管理使用者。 |
| D | 如果同盟使用者，CloudAP 外掛程式會要求 SAML 權杖，從同盟提供者，利用使用者的認證。 一旦它收到，SAML 權杖中，它會要求從 Azure AD 的 nonce。 如果使用者受管理，CloudAP 會直接從 Azure AD 取得的 nonce。 |
| E | CloudAP 外掛程式建構使用者的認證、 nonce 以及現有 PRT 的驗證要求、 使用工作階段金鑰簽署要求，並將它傳送至 Azure AD。 在同盟環境中，CloudAP 外掛程式會使用同盟提供者，而不是使用者所傳回的 SAML 語彙基元 ' 認證。 |
| F | Azure AD 會藉由比較針對內嵌在 PRT 的工作階段金鑰來驗證工作階段金鑰簽章、 驗證 nonce 並確認裝置是有效的租用戶中並發出新的 PRT。 之前所示，PRT 會再附上加密傳輸索引鍵 (tkpub) 的工作階段金鑰。 |
| G | CloudAP 外掛程式會將加密的 PRT 和工作階段金鑰傳遞至 CloudAP。 CloudAP 要求 TPM 使用的傳輸索引鍵 (tkpriv) 工作階段金鑰解密，並重新加密使用 TPM 自己的金鑰。 CloudAP PRT 以及其快取中儲存加密的工作階段金鑰。 |

### <a name="prt-usage-during-app-token-requests"></a>應用程式的權杖要求期間的 PRT 使用量

![應用程式的權杖要求期間的 PRT 使用量](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 應用程式 （例如 Outlook、 OneNote 等） 會起始 WAM 的權杖要求。 WAM，反而會要求 Azure AD WAM 外掛程式權杖的要求提供服務。 |
| B | 如果已使用應用程式的重新整理權杖，則 Azure AD WAM 外掛程式會使用它來要求存取權杖。 若要提供證明裝置繫結，WAM 外掛程式會使用工作階段金鑰來簽署要求。 Azure AD 驗證的工作階段金鑰，然後發出存取權杖和新的重新整理權杖的應用程式中，然後再由工作階段金鑰加密。 WAM 外掛程式要求來解密權杖，雲端 AP 外掛程式會接著要求 TPM 使用工作階段金鑰，導致 WAM 外掛程式取得這兩個權杖解密。 接下來，會 WAM 外掛程式提供只有應用程式的存取權杖，而它會使用 DPAPI 的重新整理權杖的重新加密，並將它儲存在它自己的快取  |
| C |  如果找不到應用程式的重新整理權杖，則 Azure AD WAM 外掛程式會使用 PRT 來要求存取權杖。 若要提供所有權的證明，WAM 外掛程式會簽署包含 PRT 的工作階段金鑰的要求。 Azure AD 會藉由比較針對內嵌在 PRT 的工作階段金鑰來驗證工作階段金鑰簽章，確認裝置有效，並發出存取權杖和重新整理權杖的應用程式。 此外，Azure AD 可以發出新 PRT，（根據重新整理循環），全部都由工作階段金鑰加密。 |
| D | WAM 外掛程式要求來解密權杖，雲端 AP 外掛程式會接著要求 TPM 使用工作階段金鑰，導致 WAM 外掛程式取得這兩個權杖解密。 接下來，WAM 外掛程式提供只有應用程式的存取權杖，而它會使用 DPAPI 的重新整理權杖的重新加密，並將它儲存在它自己的快取中。 WAM 外掛程式會使用此應用程式，接下來的重新整理權杖。 WAM 外掛程式也可讓回新的 PRT 雲端 AP 外掛程式，讓 PRT 與更新它自己的快取中之前的 Azure AD 進行驗證。 雲端 AP 外掛程式會使用新的 PRT 從現在開始。 |
| E | WAM WAM，接著，提供它傳回給呼叫的應用程式中提供的新簽發的存取權杖|

### <a name="browser-sso-using-prt"></a>瀏覽器 SSO 使用 PRT

![瀏覽器 SSO 使用 PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 步驟 | 描述 |
| :---: | --- |
| A | 使用者登入 Windows 認證，才能讓 PRT 使用。 一旦使用者開啟瀏覽器，瀏覽器 （或延伸模組） 會從登錄載入 Url。 |
| B | 當使用者開啟的 Azure AD 登入 URL 時，瀏覽器或延伸模組會將 URL 驗證與從登錄取得的項目中。 如果兩者相符，瀏覽器叫用原生用戶端主機可用來取得權杖。 |
| C | 原生用戶端主機會驗證 Url 屬於 Microsoft 身分識別提供者 （Microsoft 帳戶或 Azure AD）、 擷取 nonce，寄件者的 URL，並對 CloudAP 外掛程式呼叫 get PRT cookie。 |
| D | CloudAP 外掛程式建立 PRT cookie、 使用 TPM 繫結工作階段金鑰登入，並將它傳送回原生用戶端主機。 Cookie 由工作階段金鑰簽署，因為它無法被竄改。 |
| E | 原生用戶端主機會將此 PRT cookie 返回瀏覽器中，其中包含從 Azure AD 中稱為 x-ms-RefreshTokenCredential 和要求權杖的要求標頭的一部分。 |
| F | Azure AD 驗證 PRT cookie 工作階段金鑰簽章、 驗證 nonce，並確認裝置是有效的租用戶，並發出識別碼權杖的 web 網頁和瀏覽器的加密工作階段 cookie。 |

## <a name="next-steps"></a>後續步驟

如需有關如何疑難排解 PRT 相關問題的詳細資訊，請參閱文章[疑難排解混合式 Azure Active Directory 已加入 Windows 10 和 Windows Server 2016 裝置](troubleshoot-hybrid-join-windows-current.md)。
