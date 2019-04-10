---
title: 如何使用 Azure Active Directory 中的條件式存取的雲端應用程式存取要求應用程式保護原則 |Microsoft Docs
description: 了解如何使用 Azure Active Directory 中的條件式存取的雲端應用程式存取所需應用程式保護原則。
services: active-directory
keywords: 應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288497"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>作法：使用條件式存取 （預覽） 存取雲端應用程式需要應用程式保護原則

您的員工使用行動裝置來處理個人和工作事務。 在維護員工的生產力時，您也希望能預防資料遺失。 使用 Azure Active Directory (Azure AD) 條件式存取，您可以藉由限制存取您的雲端應用程式，第一次有應用程式保護原則的用戶端應用程式保護公司資料。

本主題說明如何設定可要求資料的存取權之前的應用程式保護原則的條件式存取原則。

## <a name="overview"></a>概觀

透過 [Azure AD 應用程式型條件式存取](overview.md)，您可以微調授權使用者如何存取您的資源。 例如，您可以將雲端應用程式的存取限制為受信任的裝置。

您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來協助保護公司資料。 Intune 應用程式保護原則不需要行動裝置管理 (MDM) 解決方案，因此不論您是否在裝置管理解決方案中註冊裝置，都可以保護公司的資料。

Azure Active Directory 條件式存取會限制存取您的雲端應用程式，Intune 已回報給 Azure AD 接收的應用程式保護原則的用戶端應用程式。 比方說，您可以限制 Exchange online 的存取，Outlook 應用程式具有 Intune 應用程式保護原則。

在條件式存取術語中，這些用戶端應用程式已知與受保護的原則**應用程式保護原則**。  

![條件式存取](./media/app-protection-based-conditional-access/05.png)

如需原則的受保護的用戶端應用程式，請參閱[應用程式保護原則需求](technical-reference.md#approved-client-app-requirement)。

您可以結合其他原則的應用程式保護基礎的條件式存取原則，例如[裝置型條件式存取原則](require-managed-devices.md)來保護個人和公司裝置資料的方式更具彈性。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>應用程式保護型條件式存取需求的優點

類似於合規性報告適用於 iOS 和 Android 的 Intune 受管理的裝置，Intune 現在報表至 Azure AD 如果應用程式保護原則套用，讓條件式存取可以將這個當做存取檢查。 這個新的條件式存取原則**應用程式保護原則**可提高安全性，例如抵禦系統錯誤：

- 不需要 Intune 授權的使用者
- 無法接收 Intune 應用程式保護原則的使用者
- Intune 應用程式保護原則的應用程式不接收原則已設定


## <a name="before-you-begin"></a>開始之前

本主題假設您已熟悉：

- [應用程式保護原則需求](technical-reference.md#app-protection-policy-requirement)技術參考。

- [核准的用戶端應用程式需求](technical-reference.md#approved-client-app-requirement)技術參考。

- [Azure Active Directory 中的條件式存取](overview.md)的基本概念。

- 如何[設定條件式存取原則](app-based-mfa.md)。


## <a name="prerequisites"></a>必要條件

若要建立應用程式保護型條件式存取原則，您必須
- 有 Enterprise Mobility + Security 或 Azure Active Directory premium 訂用帳戶 + Intune
- 確保使用者會獲得 EMS 或 Azure AD 的授權 + Intune
- 請確定已接收的應用程式保護原則的 Intune 中設定用戶端應用程式
- 請確定使用者進行設定，以接收 Intune 應用程式保護原則的 Intune 中

## <a name="app-protection-based-policy-for-exchange-online"></a>Exchange Online 的應用程式保護型原則

本案例包含存取 Exchange Online 的應用程式保護型條件式存取原則。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 將使用 iOS 或 Android 原生郵件應用程式的電子郵件設定為連線到 Exchange

- 收到一封電子郵件，郵件顯示僅能使用 Outlook 應用程式存取

- 透過連結下載應用程式

- 開啟 Outlook 應用程式並使用 Azure AD 認證登入

- 系統提示安裝 Authenticator (iOS) 或公司入口網站 (Android) 以繼續進行

- 安裝應用程式，而且可以返回 Outlook 應用程式繼續進行

- 系統提示註冊裝置

- 可以接收 Intune 應用程式保護原則

- 能夠存取電子郵件

任何 Intune 應用程式保護原則必須在應用程式存取公司資料，並可能會提示使用者重新啟動應用程式，請使用其他 pin 碼等 （若已設定的應用程式 」 和 「 平台）。

### <a name="configuration"></a>組態

**步驟 1-設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/01.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 作為雲端應用程式。

    ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. **條件：** 您必須設定 [裝置平台] 和 [用戶端應用程式] 作為 [條件]：

    a. [裝置平台] 請選取 [Android] 和 [iOS]。

    ![條件式存取](./media/app-protection-based-conditional-access/03.png)

    b. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式與傳統型應用程式] 和 [新式驗證用戶端]。

    ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. 作為**存取控制**，您必須能夠**需要應用程式保護原則 （預覽）** 選取。

    ![條件式存取](./media/app-protection-based-conditional-access/05.png)
 

**步驟 2-設定 Exchange online 使用 Active Sync (EAS) 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。


3. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 作為雲端應用程式。

    ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. **條件：** 您必須設定 [用戶端應用程式 (預覽)] 作為 [條件]。 

    a. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。

    ![條件式存取](./media/app-protection-based-conditional-access/92.png)

    b. 作為**存取控制**，您必須能夠**需要應用程式保護原則 （預覽）** 選取。

    ![條件式存取](./media/app-protection-based-conditional-access/05.png)


**步驟 3-設定適用於 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**


![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>適用於 Exchange Online 的應用程式保護型或相容裝置原則

此案例包含存取 Exchange Online 的應用程式保護型或相容裝置條件式存取原則。


### <a name="scenario-playbook"></a>情節腳本

此情節假設：
 
- 某些使用者已註冊 （不論有無公司的裝置）

- 未使用應用程式保護的應用程式向 Azure AD 申請和註冊的使用者需要註冊裝置，才能存取資源

- 已使用應用程式保護的應用程式完成申請的使用者不需要重新註冊裝置

- 使用者可以接收 Intune 應用程式保護原則如果不註冊

- 使用者可以存取 Outlook 與 Intune 應用程式保護原則的電子郵件如果不註冊

- 如果裝置已註冊，使用者可以存取 outlook 電子郵件


### <a name="configuration"></a>組態

**步驟 1-設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/62.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 作為雲端應用程式。 

     ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. **條件：** 您必須設定 [裝置平台] 和 [用戶端應用程式] 作為 [條件]。 
 
    a. [裝置平台] 請選取 [Android] 和 [iOS]。

    ![條件式存取](./media/app-protection-based-conditional-access/03.png)

    b. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [新式驗證用戶端]。

    ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. [存取控制項] 需要選取下列項目：

   - **裝置需要標記為符合規範**

   - **需要應用程式保護原則 (預覽)**

   - **需要其中一個選取的控制項**   
 
     ![條件式存取](./media/app-protection-based-conditional-access/11.png)



**步驟 2-設定 Exchange online 使用 Active Sync (EAS) 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 作為雲端應用程式。 

    ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. **條件：** 您必須設定 [用戶端應用程式] 作為 [條件]。 

    [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。

    ![條件式存取](./media/app-protection-based-conditional-access/92.png)

5. [存取控制項] 需要選取下列項目：

   - **裝置需要標記為符合規範**

   - **需要應用程式保護原則 (預覽)**

   - **需要其中一個選取的控制項**   
    ![條件式存取](./media/app-protection-based-conditional-access/11.png)



**步驟 3-設定適用於 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**


![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>適用於 Exchange Online 的應用程式保護型和相容裝置原則

此案例包含存取 Exchange Online 的應用程式保護基礎和相容裝置條件式存取原則。


### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：
 
-   將使用 iOS 或 Android 原生郵件應用程式的電子郵件設定為連線到 Exchange
-   接收指出存取權要求註冊您裝置的電子郵件
-   下載公司入口網站並登入公司入口網站
-   檢查電子郵件，並且被要求使用 Outlook 應用程式
-   下載 Outlook 應用程式
-   開啟 Outlook 應用程式，並輸入申請時所用的認證
-   能夠接收以接收 Intune 應用程式保護原則
-   能夠存取 Outlook 與 Intune 應用程式保護原則的電子郵件

任何 Intune 應用程式保護原則會啟動之前存取公司資料，而且可能會提示使用者重新啟動應用程式，請使用其他 pin 碼等 （若已設定的應用程式 」 和 「 平台）


### <a name="configuration"></a>組態

**步驟 1-設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/01.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 作為雲端應用程式。 

     ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. **條件：** 您必須設定 [裝置平台] 和 [用戶端應用程式] 作為 [條件]。 
 
    a. [裝置平台] 請選取 [Android] 和 [iOS]。

    ![條件式存取](./media/app-protection-based-conditional-access/03.png)

    b. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式與傳統型應用程式] 和 [新式驗證用戶端]。

    ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. [存取控制項] 需要選取下列項目：

   - **裝置需要標記為符合規範**

   - **需要應用程式保護原則 (預覽)**

   - **需要所有選取的控制項**   
 
     ![條件式存取](./media/app-protection-based-conditional-access/13.png)



**步驟 2-設定 Exchange online 使用 Active Sync (EAS) 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 作為雲端應用程式。 

    ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. **條件：** 您必須設定 [用戶端應用程式 (預覽)] 作為 [條件]。 

    [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式和桌面用戶端] 和 [Exchange ActiveSync 用戶端]。

    ![條件式存取](./media/app-protection-based-conditional-access/92.png)

5. [存取控制項] 需要選取下列項目：

   - **裝置需要標記為符合規範**

   - **需要核准的用戶端應用程式 （預覽）**

   - **需要所有選取的控制項**   
 
     ![條件式存取](./media/app-protection-based-conditional-access/13.png)




**步驟 3-設定適用於 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**


![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 和 SharePoint Online 的應用程式保護或應用程式架構原則

此案例包含存取 Exchange Online 和 SharePoint Online 的應用程式保護型 」 或 「 已核准的應用程式原則。


### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 會設定為其中一個用戶端應用程式上支援應用程式保護原則需求或已核准應用程式需求的應用程式清單。  
- 使用者會使用用戶端應用程式符合應用程式保護原則需求可以接收 Intune 應用程式保護原則
- 使用者會使用符合已核准應用程式原則需求，支援 Intune 應用程式保護原則的用戶端應用程式
- 開啟應用程式存取電子郵件或文件
- 開啟 Outlook 應用程式並使用 Azure AD 認證登入
- 會提示安裝 Authenticator (iOS) 或公司入口網站 (Android) 以繼續 （若未安裝）
- 安裝應用程式，而且可以返回 Outlook 應用程式繼續進行
- 系統提示註冊裝置
- 可以接收 Intune 應用程式保護原則
- 能夠存取 Outlook 與 Intune 應用程式保護原則的電子郵件
- 是無法存取站台/文件不是在應用程式保護原則需求的應用程式，但已核准的應用程式需求中所列。

任何 Intune 應用程式保護原則必須具備，才能存取公司資料，而且可能會提示使用者重新啟動應用程式，請使用其他 pin 碼等 （若已設定的應用程式 」 和 「 平台）

**備註**

- 您可以使用這種情況下，如果您想要支援這兩個應用程式保護型和應用程式為基礎的條件式存取原則。

- 在此*或是*原則、 應用程式**應用程式保護原則**需求會評估進行存取之前先**核准的用戶端應用程式**需求。

### <a name="configuration"></a>組態

**步驟 1-設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/62.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：** 您必須選取 [Office 365 Exchange Online] 作為雲端應用程式。 

     ![條件式存取](./media/app-protection-based-conditional-access/02.png)

4. **條件：** 您必須設定 [裝置平台] 和 [用戶端應用程式] 作為 [條件]。 
 
    a. [裝置平台] 請選取 [Android] 和 [iOS]。

    ![條件式存取](./media/app-protection-based-conditional-access/03.png)

    b. [用戶端應用程式 (預覽)] 需選取 [行動裝置應用程式與傳統型應用程式] 和 [新式驗證用戶端]。

    ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. [存取控制項] 需要選取下列項目：

   - **需要已核准的用戶端應用程式**

   - **需要應用程式保護原則 (預覽)**

   - **需要其中一個選取的控制項**   
 
     ![條件式存取](./media/app-protection-based-conditional-access/12.png)


**步驟 2-設定適用於 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**


![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。




## <a name="next-steps"></a>後續步驟

如果您想要知道如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](app-based-mfa.md)。

如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。 