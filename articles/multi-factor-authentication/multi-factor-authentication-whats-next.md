---
title: "設定 Azure Multi-factor Authentication |Microsoft 文件"
description: "本文說明如何設定 Azure Multi-factor Authentication 報告、 詐騙警示、 一次性略過自訂語音訊息，快取中，信任的 Ip，以及應用程式密碼。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4421b995e69e115fbb6c7379af79aaef537aed0d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2018
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>設定 Azure Multi-Factor Authentication 設定

這篇文章可協助您管理 Azure Multi-factor Authentication，既然您已啟動且正在執行。 本文涵蓋各種主題，協助您充分發揮 Azure Multi-Factor Authentication 的功效。 並非所有的功能都可在每個[版本的 Azure Multi-factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need)。

| 功能 | 說明 | 
|:--- |:--- |
| [封鎖及解除封鎖使用者](#block-and-unblock-users) |若要防止使用者接收驗證要求中使用封鎖/解除封鎖使用者的功能。 |
| [詐騙警示](#fraud-alert) |設定詐騙警示功能，讓您的使用者可以報告詐騙，嘗試存取他們的資源。 |
| [一次性略過](#one-time-bypass) |若要允許使用者驗證一次所使用的單次許可功能_略過_Multi-factor Authentication。 |
| [自訂語音訊息](#custom-voice-messages) |使用您自己的記錄或 greetings Multi-factor Authentication 搭配使用自訂語音訊息功能。 |
| [快取](#caching-in-azure-multi-factor-authentication) |設定在特定時間週期，以便後續驗證嘗試成功，會自動使用的快取功能。 |
| [信任的 IP](#trusted-ips) |受管理或同盟租用戶的系統管理員可以使用信任的 Ip 功能，略過兩步驟驗證的使用者從公司內部網路登入。 |
| [應用程式密碼](#app-passwords) |使用應用程式密碼功能，讓應用程式略過多重要素驗證，並繼續進行。 |
| [針對受信任的裝置和瀏覽器記住多重要素驗證](#remember-multi-factor-authentication-for-trusted-devices) |此功能可用來設定天數之後使用 Multi-factor Authentication 成功登入的使用者具有記得受信任的裝置和瀏覽器。 |
| [可選取的驗證方法](#selectable-verification-methods) |此功能可用來選取的使用者可以使用的驗證方法清單。 |

## <a name="block-and-unblock-users"></a>封鎖及解除封鎖使用者

使用_封鎖及解除封鎖使用者_功能，以防止使用者接收驗證要求。 系統會自動拒絕已封鎖使用者的任何驗證嘗試。 使用者會保留已封鎖它們遭到封鎖的時間的 90 天。

### <a name="block-a-user"></a>封鎖使用者

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至**Azure Active Directory** > **MFA Server** > **封鎖/解除封鎖使用者**。
3. 選取**新增**封鎖使用者。
4. 選取**複寫群組**。 輸入使用者名稱所封鎖的使用者**username<span></span>@domain.com**。輸入中的註解**原因**欄位。
5. 選取**新增**完成封鎖使用者。

### <a name="unblock-a-user"></a>解除封鎖使用者

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至**Azure Active Directory** > **MFA Server** > **封鎖/解除封鎖使用者**。
3. 選取**解除封鎖**中**動作**解除封鎖之使用者旁邊的資料行。
4. 在 [解除封鎖的原因] 欄位中輸入註解。
5. 選取**解除封鎖**完成解除封鎖使用者。

## <a name="fraud-alert"></a>詐騙警示

設定_詐騙警示_功能，讓您的使用者可以報告詐騙，嘗試存取他們的資源。 使用者可以在使用行動裝置應用程式或透過電話報告詐騙的嘗試。

### <a name="turn-on-fraud-alerts"></a>開啟 詐騙警示

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至**Azure Active Directory** > **MFA Server** > **詐騙警示**。

   ![開啟 詐騙警示](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. 設定**允許使用者提交詐騙警示**設**上**。
4. 選取 [ **儲存**]。

### <a name="configuration-options"></a>組態選項

- **回報詐騙時封鎖使用者**: 90 天，或直到系統管理員會解除封鎖其帳戶，如果使用者回報詐騙時，會封鎖其帳戶。 系統管理員可以使用登入報表，檢閱登入，並採取適當的動作，以避免未來的詐騙。 系統管理員接著可以[解除封鎖](#unblock-a-user)使用者的帳戶。
- **程式碼，以在初始問候期間報告詐騙**： 當使用者接聽電話來執行兩步驟驗證時，它們通常按 **#** 以確認其登入。 回報詐騙使用者輸入的程式碼之前按 **#** 。 根據預設，此代碼是 **0**，但您可以自訂。

  >[!NOTE]
  >從 Microsoft 預設語音問候語指示使用者按下**&#0;**提交詐騙警示。 如果您想要使用的程式碼以外**0**、 記錄和上傳您自己的自訂語音問候語與適當的指示，為您的使用者。
  >

### <a name="view-fraud-reports"></a>檢視詐騙報告

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**Azure Active Directory** > **登入**。詐騙報告現在是標準的 Azure AD 登入報表的一部分。

## <a name="one-time-bypass"></a>一次性略過

_一次性略過_功能可讓使用者驗證一次，而不需執行兩步驟驗證。 許可只是暫時性，經過指定的秒數之後就會到期。 在其中的行動裝置應用程式或電話未收到通知或電話的情況下，您可以允許一次性略過，讓使用者能夠存取所需的資源。

### <a name="create-a-one-time-bypass"></a>建立單次許可

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至**Azure Active Directory** > **MFA Server** > **一次性略過**。

   ![建立單次許可](./media/multi-factor-authentication-whats-next/onetimebypass.png)

3. 選取 [新增] 。
4. 如有必要，請選取略過的複寫群組。
5. 輸入使用者**username<span></span>@domain.com**。輸入許可的效力應持續的秒數。 輸入許可的原因。 
6. 選取 [新增] 。 時間限制立即會生效。 使用者需要一次性略過到期之前登入。 

### <a name="view-the-one-time-bypass-report"></a>檢視單次許可報告

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至**Active Directory** > **MFA Server** > **一次性略過**。

## <a name="custom-voice-messages"></a>自訂語音訊息
您可以使用您自己的記錄或 greetings 進行兩步驟驗證與_自訂語音訊息_功能。 在加入或取代 Microsoft 錄製，則可以使用這些訊息。

開始之前，請留意下列限制：

* 支援的檔案格式為 .wav 和 .mp3。
* 檔案大小限制為 5 MB。
* 驗證訊息應該少於 20 秒。 超過 20 秒的訊息可能會導致驗證失敗。 在訊息結束，並驗證逾時之前，使用者可能無法回應。

### <a name="set-up-a-custom-message"></a>設定自訂訊息

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至**Azure Active Directory** > **MFA Server** > **通話設定**。

   ![記錄自訂號碼的訊息](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. 選取 [新增問候語]。
4. 選擇問候語的類型。 選擇的語言。
5. 選取要上傳的 .mp3 或 .wav 音效檔。
6. 選取 [新增] 。

## <a name="caching-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的快取

您可以設定要允許驗證嘗試次數之後使用者已驗證所使用, 的時段_快取_功能。 中指定的使用者後續驗證嘗試期間成功自動。 主要使用快取，當第一個要求仍在進行中時，在內部部署系統，例如 VPN，會傳送多個驗證要求。 這項功能可讓使用者成功進行中的第一個驗證之後，成功的後續要求。 

>[!NOTE]
>快取功能的目的不是要用於登入至 Azure Active Directory (Azure AD)。

### <a name="set-up-caching"></a>設定快取 

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至**Azure Active Directory** > **MFA Server** > **快取規則**。

   ![設定快取規則](./media/multi-factor-authentication-whats-next/cachingrules.png)

3. 選取 [新增] 。
4. 選取**快取類型**從下拉式清單。 輸入的最大數目**快取秒**。 
5. 如有必要，請選取驗證類型，並指定應用程式。 
6. 選取 [新增] 。


## <a name="trusted-ips"></a>信任的 IP

_信任的 Ip_之受管理或同盟租用戶系統管理員使用 Azure Multi-factor Authentication Server 的功能。 此功能會略過雙步驟驗證的使用者從公司內部網路登入。 功能是適用於完整版本的 Azure Multi-factor Authentication，而非系統管理員可用版本。 如需有關如何取得完整版 Azure Multi-Factor Authentication 的詳細資訊，請參閱[ Azure Multi-Factor Authentication](multi-factor-authentication.md)。

| Azure AD 租用戶類型 | 受信任的 Ip 功能選項 |
|:--- |:--- |
| 受控 |**特定的 IP 位址範圍**： 系統管理員指定的 IP 位址範圍，可以略過的雙步驟驗證的使用者從公司內部網路登入。|
| 同盟 |**所有同盟使用者**： 所有同盟的使用者登入從組織內部的可以略過雙步驟驗證。 使用者的 Active Directory Federation Services (AD FS) 所發出的宣告略過驗證。<br/>**特定的 IP 位址範圍**： 系統管理員指定的 IP 位址範圍，可以略過的雙步驟驗證的使用者從公司內部網路登入。 |

信任的 Ip 略過只能從公司內部網路內的運作方式。 如果您選取**所有同盟使用者**選項和使用者登入時從公司內部網路之外，使用者必須使用兩步驟驗證進行驗證。 即使該使用者提供 AD FS 宣告程序都是相同的。 

**在公司網路內的使用者體驗**

停用 「 信任的 Ip 」 功能時，兩步驟驗證是必要的瀏覽器流程。 應用程式密碼所需的較舊的豐富型用戶端應用程式。 

啟用信任的 Ip 功能時，兩步驟驗證是*不*瀏覽器流程需要。 應用程式密碼*不*所需的較舊的豐富型用戶端應用程式，前提是尚未建立應用程式密碼的使用者。 正在使用中的應用程式密碼之後，密碼會保留必要的。 

**公司網路外部的使用者體驗**

不論是否啟用信任的 Ip 功能，雙步驟驗證，才能瀏覽器流程。 應用程式密碼所需的較舊的豐富型用戶端應用程式。 

### <a name="enable-named-locations-by-using-conditional-access"></a>使用條件式存取啟用具名的位置

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側選取**Azure Active Directory** > **條件式存取** > **名為位置**。
3. 選取**新位置**。
4. 輸入位置的名稱。
5. 選取**標記為受信任的位置**。
6. 輸入的 IP 範圍，以 CIDR 標記法，例如**192.168.1.1/24**。
7. 選取 [建立] 。

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>啟用信任的 Ip 功能使用條件式存取

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側選取**Azure Active Directory** > **條件式存取** > **名為位置**。
3. 選取**設定 MFA 可信任 Ip**。
4. 在**服務設定**頁面的 **信任的 Ip**，選擇下列兩個選項：
   
   * **針對來自我的內部網路同盟使用者要求**： 選擇此選項，請選取核取方塊。 所有的同盟使用者使用 AD FS 所發出的宣告從公司網路登入略過的雙步驟驗證。 請確定 AD FS 有規則可用於將內部網路宣告新增至適當的流量。 如果規則不存在，請在 AD FS 中建立下列規則：<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **針對來自特定範圍之公開 Ip 的要求**： 若要選擇此選項，IP 位址在文字方塊中輸入使用 CIDR 表示法。
   
     * 是透過 xxx.xxx.xxx.254 xxx.xxx.xxx.1 的 IP 位址，使用標記法類似**xxx.xxx.xxx.0/24**。
     * 以單一 IP 位址，使用標記法類似**xxx<span></span>.xxx.xxx.xxx/32**。
     
     輸入最多可到 50 個 IP 位址範圍。 從這些 IP 位址登入的使用者會略過雙步驟驗證。

5. 選取 [ **儲存**]。

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>啟用信任的 Ip 功能，使用服務設定

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [Azure Active Directory] > [使用者和群組] > [所有使用者]。
3. 選取 [多重要素驗證]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在**服務設定**頁面的 **信任的 Ip**，選擇下列兩個選項：
   
   * **針對來自我的內部網路上的同盟使用者要求**： 選擇此選項，請選取核取方塊。 所有的同盟使用者使用 AD FS 所發出的宣告從公司網路登入略過的雙步驟驗證。 請確定 AD FS 有規則可用於將內部網路宣告新增至適當的流量。 如果規則不存在，請在 AD FS 中建立下列規則：<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **從指定的 IP 位址子網路的要求**： 若要選擇此選項，IP 位址在文字方塊中輸入使用 CIDR 表示法。 
     
     * 是透過 xxx.xxx.xxx.254 xxx.xxx.xxx.1 的 IP 位址，使用標記法類似**xxx.xxx.xxx.0/24**。
     * 以單一 IP 位址，使用標記法類似**xxx<span></span>.xxx.xxx.xxx/32**。
     
     輸入最多可到 50 個 IP 位址範圍。 從這些 IP 位址登入的使用者會略過雙步驟驗證。

6. 選取 [ **儲存**]。

![啟用服務設定 信任的 Ip](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>應用程式密碼

某些應用程式，例如 Office 2010 或更早版本和 Apple Mail 不支援雙步驟驗證。 應用程式不會設定成接受第二個驗證。 若要使用這些應用程式，以善用_應用程式密碼_功能。 您可以使用傳統密碼取代的應用程式密碼，以允許應用程式略過雙步驟驗證，並繼續工作。

>[!NOTE]
>新式驗證及更新版本的 Microsoft Office 2013 用戶端
> 
>Office 2013 用戶端和更新版本 （包括 Outlook)，支援新式驗證通訊協定，並且能夠使用雙步驟驗證。 啟用用戶端之後，應用程式密碼並不需要用戶端。 如需詳細資訊，請參閱 [發表 Office 2013 新式驗證公開預覽](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。
>

### <a name="considerations-about-app-passwords"></a>關於應用程式密碼考量

當使用應用程式密碼，請考慮下列要點：

* 每個應用程式，應用程式密碼是只能輸入一次。 使用者不需要追蹤密碼，或輸入每一次。
* 實際的密碼會自動產生，而不是由使用者提供。 攻擊者比較難以猜中自動產生的密碼，所以比較安全。
* 每位使用者的密碼以 40 組為限。 
* 快取密碼並在內部部署案例中使用它們的應用程式可以啟動失敗，因為應用程式密碼不工作或學校帳戶以外得知。 此案例的範例是內部部署的 Exchange 電子郵件，但封存的郵件是在雲端中。 在此案例中，不適用於相同的密碼。
* 上的使用者帳戶啟用多重要素驗證之後，應用程式密碼可與最非瀏覽器用戶端，例如 Outlook 和 Microsoft Skype for Business。 無法使用應用程式密碼透過非瀏覽器應用程式，例如 Windows PowerShell 中執行系統管理動作。 無法執行動作，即使使用者具有系統管理員帳戶。 若要執行 PowerShell 指令碼，建立服務帳戶具有強式密碼並不啟用兩步驟驗證的帳戶。

>[!WARNING]
>應用程式密碼無法在用戶端會同時與內部部署及雲端自動探索端點通訊的混合環境作用。 網域密碼，才能驗證內部部署。 應用程式密碼，才能在雲端驗證。
>

### <a name="guidance-for-app-password-names"></a>應用程式密碼名稱的指引

應用程式密碼名稱應反映正在使用的裝置。 如果您的膝上型電腦有非瀏覽器應用程式，例如 Outlook、 Word 和 Excel，請建立一個名為的應用程式密碼**膝上型電腦**這些應用程式。 建立名為另一個應用程式密碼**桌面**在桌面的電腦上執行的相同應用程式。 

>[!NOTE]
>我們建議您建立每個裝置，一個應用程式密碼，而不是每個應用程式的一個應用程式密碼。

### <a name="federated-or-single-sign-on-app-passwords"></a>同盟或單一登入應用程式密碼

Azure AD 支援同盟，或單一登入 (SSO)，與在內部部署 Windows Server Active Directory 網域服務 (AD DS)。 如果貴組織已與 Azure AD 同盟，而且您使用 Azure Multi-factor Authentication，請考慮下列有關應用程式密碼的重點。

>[!NOTE]
>下列各點僅適用於同盟 (SSO) 客戶。

* Azure ad 驗證應用程式密碼，因此，略過同盟。 只有在設定應用程式密碼時，才主動使用同盟。
* 身分識別提供者 (IdP) 未連線的同盟 (SSO) 使用者，像被動流程。 應用程式密碼會儲存在工作或學校帳戶。 如果使用者離開公司時，使用者的資訊會流向工作或學校帳戶使用**DirSync**即時。 帳戶停用/刪除可能需要三小時的時間來同步處理，而這可能會延遲在 Azure AD 中應用程式密碼停用/刪除。
* 應用程式密碼功能不接受在內部部署用戶端存取控制設定。
* 在內部部署驗證記錄/稽核功能適用於應用程式密碼功能搭配使用。
* 某些進階的架構需要與用戶端的雙步驟驗證的認證組合。 這些認證可以包含的工作或學校帳戶使用者名稱和密碼，以及應用程式密碼。 需求取決於如何執行驗證。 針對在內部部署基礎結構、 工作或學校帳戶使用者名稱和必要的密碼進行驗證的用戶端。 對 Azure AD 進行驗證的用戶端，應用程式密碼是必要的。

  例如，假設您有下列架構：

  * 在內部部署執行個體的 Active Directory 與 Azure AD 同盟。
  * 您正在使用 Exchange online。
  * 您正在使用 Skype 商務內部部署。
  * 您使用 Azure Multi-factor Authentication。

  ![使用同盟的組織中的應用程式密碼](./media/multi-factor-authentication-whats-next/federated.png)

  在此案例中，您可以使用下列認證：

  * 若要登入商務用 Skype，使用您的公司或學校帳戶使用者名稱和密碼。
  * 若要從連線到 Exchange online 的 Outlook 用戶端存取通訊錄，請使用應用程式密碼。

### <a name="allow-users-to-create-app-passwords"></a>允許使用者建立應用程式密碼

根據預設，使用者無法建立應用程式密碼。 必須啟用應用程式密碼的功能。 若要讓使用者能夠建立應用程式密碼，使用下列程序：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [Azure Active Directory] > [使用者和群組] > [所有使用者]。
3. 選取 [多重要素驗證]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在**服務設定**頁面上，選取**允許使用者建立應用程式密碼以登入非瀏覽器應用程式**選項。

   ![允許使用者建立應用程式密碼](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>建立應用程式密碼

使用者可以在一開始的註冊期間建立應用程式密碼。 使用者已建立應用程式密碼的註冊程序結尾的選項。

使用者也可以在註冊後建立應用程式密碼。 透過 Azure 入口網站或 Office 365 入口網站中的設定，您可以變更應用程式密碼。 如需詳細資訊和詳細的步驟，為您的使用者，請參閱[什麼是 Azure Multi-factor Authentication Server 中的應用程式密碼？](./end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>受信任的裝置記住多重要素驗證
_記住多重要素驗證_裝置和使用者所信任的瀏覽器的功能是所有 Multi-factor Authentication 使用者的可用功能。 使用者可以略過在指定天數之後他們已成功登入的裝置使用 Multi-factor Authentication, 的後續驗證。 此功能增強可用性的使用者必須執行相同的裝置上的兩步驟驗證的次數降到最低。

>[!IMPORTANT]
>如果遭到入侵的帳戶或裝置，受信任的裝置記住多重要素驗證可能會影響安全性。 如果公司帳戶遭入侵或信任的裝置遺失或遭竊，您應該[在所有裝置上還原 Multi-Factor Authentication](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user)。
>
>還原動作會撤銷所有的裝置，從受信任的狀態，而且需要使用者執行雙步驟驗證一次。 您也可以指示中的指示與他們的裝置上還原 Multi-factor Authentication 使用者[管理您的設定進行兩步驟驗證](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)。
>

### <a name="how-the-feature-works"></a>此功能的運作方式

記住多重要素驗證功能永續性 cookie 會在瀏覽器時設定使用者選取**不要再詢問我 X 天**在登入時的選項。 不會提示使用者一次的多重要素驗證來自該相同的瀏覽器的 cookie 到期之前。 如果使用者開啟不同的瀏覽器，在相同的裝置，或清除其 cookie，它們會提示您再次確認。 

**不要再詢問我 X 天**選項並不會顯示非瀏覽器應用程式，不論應用程式是否支援新式驗證。 這些應用程式使用_重新整理權杖_可提供新的存取權杖每隔一小時。 當重新整理權杖已經驗證時，Azure AD 會檢查指定天數內發生的最後一個兩步驟驗證。 

此功能會減少驗證 web 應用程式，通常提示每次數目。 此功能會增加通常提示每隔 90 天的數據機驗證用戶端的驗證次數。

>[!IMPORTANT]
>**記住多重要素驗證**功能與不相容**讓我保持登**AD FS 中，當使用者透過 Azure Multi-factor Authentication AD fs 執行雙步驟驗證的功能驗證伺服器或協力廠商的多因素驗證解決方案。
>
>如果您的使用者選取**讓我保持登**在 AD FS 以及他們的裝置做為受信任可以進行 Multi-factor Authentication 的標記，並不會自動驗證使用者之後**記住多重要素驗證**數天內到期。 Azure AD 要求全新雙步驟驗證，但是 AD FS 會傳回原始的多重要素驗證宣告和日期，而非再次執行雙步驟驗證的語彙基元。 反應設定驗證之間產生迴圈 Azure AD 與 AD FS。
>

### <a name="enable-remember-multi-factor-authentication"></a>啟用記住多重要素驗證

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [Azure Active Directory] > [使用者和群組] > [所有使用者]。
3. 選取 [多重要素驗證]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在**服務設定**頁面的 **管理記住多重要素驗證**，選取**允許使用者記住多重要素驗證就會在裝置上信任**選項。

   ![受信任的裝置記住多重要素驗證](./media/multi-factor-authentication-whats-next/remember.png)

6. 設定為允許略過兩步驟驗證的受信任的裝置的天數。 預設值為 14 天。
7. 選取 [ **儲存**]。

### <a name="mark-a-device-as-trusted"></a>將裝置標示為受信任

啟用記住多重要素驗證功能後，使用者可將標示為信任時的裝置登入時選取**不要再詢問**。

![選取 「 不要再問 「 信任的裝置](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>可選取的驗證方法

您可以選擇使用可供您使用者的驗證方法_可選取的驗證方法_功能。 下表提供一種方法的簡短概觀。

當您的使用者註冊他們的帳戶的 Azure Multi-factor Authentication 時，他們可以選擇其慣用的驗證方法從已啟用的選項。 中提供的使用者註冊程序的指導方針[設定進行兩步驟驗證我的帳戶](multi-factor-authentication-end-user-first-time.md)。

| 方法 | 說明 |
|:--- |:--- |
| 電話通話 |撥打自動語音電話。 使用者可接聽電話並按電話鍵盤上的 # 進行驗證。 電話號碼不會在內部部署 Active directory 同步處理。 |
| 電話簡訊 |傳送文字訊息，其中包含驗證碼。 系統會提示使用者在登入介面中輸入這個驗證碼。 此程序稱為「單向 SMS」。 雙向 SMS 表示使用者必須以簡訊回傳特定驗證碼。 雙向簡訊時已被取代，但不支援 2018 年 11 月 14 之後。 使用者都已設定為雙向簡訊會自動切換成_呼叫電話_當時的驗證。|
| 行動應用程式的通知 |將推播通知傳送至您的電話或已註冊的裝置。 使用者會看到通知，然後選取 [驗證] 來完成驗證。 Microsoft Authenticator 應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |
| 行動應用程式傳回的驗證碼 |Microsoft Authenticator 應用程式每隔 30 秒會產生新的 OATH 驗證碼。 使用者的登入介面中輸入驗證碼。 Microsoft Authenticator 應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |

### <a name="enable-and-disable-verification-methods"></a>啟用和停用驗證方法

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側，選取 [Azure Active Directory] > [使用者和群組] > [所有使用者]。
3. 選取 [多重要素驗證]。
4. 在 [Multi-Factor Authentication] 下，選取 [服務設定]。
5. 在**服務設定**頁面的 **驗證選項**，選取/取消選取要提供給使用者的方法。

   ![選取驗證方法](./media/multi-factor-authentication-whats-next/authmethods.png)

6. 按一下 [檔案] 。
