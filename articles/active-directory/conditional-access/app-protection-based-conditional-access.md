---
title: 使用 Azure Active Directory 中的條件式存取的雲端應用程式存取所需應用程式保護原則 |Microsoft Docs
description: 了解如何使用 Azure Active Directory 中的條件式存取的雲端應用程式存取所需應用程式保護原則。
services: active-directory
keywords: 條件式存取應用程式，與 Azure AD，安全地存取公司資源，條件式存取原則的條件式存取
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
ms.openlocfilehash: 51d209eceec2c7172cf34dc689079e669760149e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112732"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>使用條件式存取 （預覽） 存取雲端應用程式需要應用程式保護原則

您的員工使用行動裝置來處理個人和工作事務。 在維護員工的生產力時，您也希望能預防資料遺失。 使用 Azure Active Directory (Azure AD) 條件式存取，您可以藉由限制存取您雲端應用程式保護公司資料。 第一次使用應用程式保護原則的用戶端應用程式。

這篇文章說明如何設定條件式存取原則，可以要求應用程式保護原則，才能存取資料。

## <a name="overview"></a>概觀

具有[Azure AD 條件式存取](overview.md)，您可以微調授權的使用者如何存取您的資源。 例如，您可以將雲端應用程式的存取限制為受信任的裝置。

您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來協助保護公司資料。 Intune 應用程式保護原則不需要行動裝置管理 (MDM) 解決方案。 您可以保護公司資料，不論在裝置管理解決方案中註冊裝置。

Azure Active Directory 條件式存取權會限制存取您的雲端應用程式，Intune 已回報給 Azure AD 接收的應用程式保護原則的用戶端應用程式。 比方說，您可以限制 Exchange online 的存取，Outlook 應用程式具有 Intune 應用程式保護原則。

在條件式存取術語中，這些用戶端應用程式已知與受保護的原則*應用程式保護原則*。  

![條件式存取](./media/app-protection-based-conditional-access/05.png)

如需原則保護的用戶端應用程式的清單，請參閱 <<c0> [ 應用程式保護原則需求](technical-reference.md#approved-client-app-requirement)。

您可以結合其他原則，應用程式保護基礎條件式存取原則，例如[裝置型條件式存取原則](require-managed-devices.md)。 如此一來，您可以提供彈性的方式來保護個人和公司裝置資料。

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>應用程式保護為基礎的條件式存取需求的優點

類似於適用於 iOS 和 Android 的受管理的裝置，Intune 會套用至 Azure AD，如果應用程式保護原則的報表現在報告由 Intune 的合規性。 條件式存取可以使用此原則，作為存取檢查。 這個新的條件式存取原則，應用程式保護原則中，可提高安全性。 它可防止系統管理員錯誤，例如：

- 不需要 Intune 授權的使用者。
- 無法接收 Intune 應用程式保護原則的使用者。
- Intune 應用程式保護原則的應用程式未設定為接收原則。


## <a name="before-you-begin"></a>開始之前

本文假設您已熟悉以下各項：

- [應用程式保護原則需求](technical-reference.md#app-protection-policy-requirement)技術參考。
- [核准的用戶端應用程式需求](technical-reference.md#approved-client-app-requirement)技術參考。
- 基本概念[Azure Active Directory 中的條件式存取](overview.md)。
- 如何[設定條件式存取原則](app-based-mfa.md)。


## <a name="prerequisites"></a>必要條件

若要建立應用程式保護型條件式存取原則，您必須：

- 擁有 Enterprise Mobility + Security 或 Azure Active Directory premium 訂用帳戶 + Intune。
- 請確定使用者已獲授權 Enterprise Mobility + Security 或 Azure AD + Intune。
- 請確定已在 Intune 接收應用程式保護原則中設定用戶端應用程式。
- 請確定使用者進行設定，以接收 Intune 應用程式保護原則的 Intune 中。

## <a name="app-protection-based-policy-for-exchange-online"></a>Exchange Online 的應用程式保護型原則

本案例包含的應用程式保護型條件式存取原則來存取 Exchange Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 在 iOS 或 Android 上使用原生郵件應用程式，連線到 Exchange 中設定電子郵件。
- 接收指出存取可用只能藉由使用 Outlook 應用程式的電子郵件。
- 下載應用程式的連結。
- 開啟 Outlook 應用程式，並使用 Azure AD 認證登入。
- 系統會提示安裝是用於 iOS 的 Microsoft Authenticator 或 Intune 公司入口網站，供 Android 使用以繼續。
- 安裝應用程式，並傳回至 Outlook 應用程式，以繼續。
- 系統會提示註冊裝置。
- 可以接收 Intune 應用程式保護原則。
- 可以存取電子郵件。

任何 Intune 應用程式保護原則必須位於應用程式存取公司資料。 原則可能會提示使用者重新啟動應用程式，或使用其他 PIN。 如果原則已針對應用程式與平台，這會是大小寫。

### <a name="configuration"></a>組態

**步驟 1：適用於 Exchange Online 設定 Azure AD 條件式存取原則**

條件式存取原則，在此步驟中，設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/01.png)

1. 輸入您的條件式存取原則的名稱。

2. 底下**指派**，請在**使用者和群組**中，選取至少一個使用者或群組的每個條件式存取原則。

3. 在 **雲端應用程式**，選取**Office 365 Exchange Online**。

    ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. 在 **條件**，設定**裝置平台**並**用戶端應用程式 （預覽）** :

    a. 在 **裝置平台**，選取**Android**並**iOS**。

    ![條件式存取](./media/app-protection-based-conditional-access/03.png)

    b. 在 **用戶端應用程式 （預覽）** ，選取**行動裝置應用程式和桌面用戶端**並**新式驗證用戶端**。

    ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. 底下**存取控制**，選取**需要應用程式保護原則 （預覽）** 。

    ![條件式存取](./media/app-protection-based-conditional-access/05.png)
 

**步驟 2：適用於 Exchange Online 使用 ActiveSync (EAS) 設定 Azure AD 條件式存取原則**

條件式存取原則，在此步驟中，設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 輸入您的條件式存取原則的名稱。

2. 底下**指派**，請在**使用者和群組**中，選取至少一個使用者或群組的每個條件式存取原則。


3. 在 **雲端應用程式**，選取**Office 365 Exchange Online**。

    ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. 在 **條件**，設定**用戶端應用程式 （預覽）** 。 

    a. 在 **用戶端應用程式 （預覽）** ，選取**行動裝置應用程式和桌面用戶端**並**Exchange ActiveSync 用戶端**。

    ![條件式存取](./media/app-protection-based-conditional-access/92.png)

    b. 底下**存取控制**，選取**需要應用程式保護原則 （預覽）** 。

    ![條件式存取](./media/app-protection-based-conditional-access/05.png)


**步驟 3：設定適用於 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**


![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱 <<c0> [ 使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>適用於 Exchange Online 的應用程式保護型或相容裝置原則

本案例包含的應用程式保護型或相容裝置條件式存取原則來存取 Exchange Online。


### <a name="scenario-playbook"></a>情節腳本

此情節假設：
 
- 已註冊使用者，無論公司的裝置。
- 註冊裝置，才能存取資源的應用程式需要受保護的使用者未註冊，並使用應用程式與 Azure AD 註冊。
- 使用受保護的應用程式的應用程式的已註冊的使用者不需要重新註冊裝置。
- 如果沒有註冊，則使用者可以接收 Intune 應用程式保護原則。
- 如果沒有註冊，則使用者可以存取 Outlook 與 Intune 應用程式保護原則的電子郵件。
- 如果裝置已註冊，使用者可以存取 outlook 電子郵件。


### <a name="configuration"></a>組態

**步驟 1：適用於 Exchange Online 設定 Azure AD 條件式存取原則**

條件式存取原則，在此步驟中，設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/62.png)

1. 輸入您的條件式存取原則的名稱。

2. 底下**指派**，請在**使用者和群組**中，選取至少一個使用者或群組的每個條件式存取原則。

3. 在 **雲端應用程式**，選取**Office 365 Exchange Online**。 

     ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. 在 **條件**，設定**裝置平台**並**用戶端應用程式 （預覽）** 。 
 
    a. 在 **裝置平台**，選取**Android**並**iOS**。

    ![條件式存取](./media/app-protection-based-conditional-access/03.png)

    b. 在 **用戶端應用程式 （預覽）** ，選取**行動裝置應用程式和桌面用戶端**並**新式驗證用戶端**。

    ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. 底下**存取控制**，選取下列選項：

   - [裝置需要標記為合規] 

   - **需要應用程式保護原則 （預覽）**

   - [需要其中一個選取的控制項]    
 
     ![條件式存取](./media/app-protection-based-conditional-access/11.png)



**步驟 2：搭配 ActiveSync 為 Exchange Online 設定 Azure AD 條件式存取原則**

條件式存取原則，在此步驟中，設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 輸入您的條件式存取原則的名稱。

2. 底下**指派**，請在**使用者和群組**中，選取至少一個使用者或群組的每個條件式存取原則。

3. 在 **雲端應用程式**，選取**Office 365 Exchange Online**。 

    ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. 在 **條件**，設定**用戶端應用程式 （預覽）** 。 

    在 **用戶端應用程式 （預覽）** ，選取**行動裝置應用程式和桌面用戶端**並**Exchange ActiveSync 用戶端**。

    ![條件式存取](./media/app-protection-based-conditional-access/92.png)

5. 底下**存取控制**，選取下列選項：

   - [裝置需要標記為合規] 

   - **需要應用程式保護原則 （預覽）**

   - [需要其中一個選取的控制項] 

     ![條件式存取](./media/app-protection-based-conditional-access/11.png)



**步驟 3：設定適用於 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**


![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱 <<c0> [ 使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>適用於 Exchange Online 的應用程式保護型和相容裝置原則

本案例包含應用程式保護基礎且符合規範裝置的條件式存取原則來存取 Exchange Online。


### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：
 
-   在 iOS 或 Android 上使用原生郵件應用程式，連線到 Exchange 中設定電子郵件。
-   接收指出存取需要註冊其裝置的電子郵件。
-   下載 Intune 公司入口網站並登入入口網站。
-   檢查電子郵件，並要求使用 Outlook 應用程式。
-   下載 Outlook 應用程式。
-   開啟 Outlook 應用程式，並輸入申請時所用的認證。
-   可以接收 Intune 應用程式保護原則。
-   可以存取 Outlook 與 Intune 應用程式保護原則的電子郵件。

任何 Intune 應用程式保護原則會啟動，才能存取公司資料。 原則可能會提示使用者重新啟動應用程式，或使用其他 PIN。 如果原則已針對應用程式與平台，這會是大小寫。


### <a name="configuration"></a>組態

**步驟 1：適用於 Exchange Online 設定 Azure AD 條件式存取原則**

條件式存取原則，在此步驟中，設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/01.png)

1. 輸入您的條件式存取原則的名稱。

2. 底下**指派**，請在**使用者和群組**中，選取至少一個使用者或群組的每個條件式存取原則。

3. 在 **雲端應用程式**，選取**Office 365 Exchange Online**。 

     ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. 在 **條件**，設定**裝置平台**並**用戶端應用程式 （預覽）** 。 
 
    a. 在 **裝置平台**，選取**Android**並**iOS**。

    ![條件式存取](./media/app-protection-based-conditional-access/03.png)

    b. 在 **用戶端應用程式 （預覽）** ，選取**行動裝置應用程式和桌面用戶端**並**新式驗證用戶端**。

    ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. 底下**存取控制**，選取下列選項：

   - [裝置需要標記為合規] 

   - **需要應用程式保護原則 （預覽）**

   - [需要所有選取的控制項]    
 
     ![條件式存取](./media/app-protection-based-conditional-access/13.png)



**步驟 2：搭配 ActiveSync 為 Exchange Online 設定 Azure AD 條件式存取原則**

條件式存取原則，在此步驟中，設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/06.png)

1. 輸入您的條件式存取原則的名稱。

2. 底下**指派**，請在**使用者和群組**中，選取至少一個使用者或群組的每個條件式存取原則。

3. 在 **雲端應用程式**，選取**Office 365 Exchange Online**。 

    ![條件式存取](./media/app-protection-based-conditional-access/07.png)

4. 在 **條件**，設定**用戶端應用程式 （預覽）** 。 

    在 **用戶端應用程式 （預覽）** ，選取**行動裝置應用程式和桌面用戶端**並**Exchange ActiveSync 用戶端**。

    ![條件式存取](./media/app-protection-based-conditional-access/92.png)

5. 底下**存取控制**，選取下列選項：

   - [裝置需要標記為合規] 

   - **需要應用程式保護原則 （預覽）**

   - [需要所有選取的控制項]    
 
     ![條件式存取](./media/app-protection-based-conditional-access/13.png)




**步驟 3：設定適用於 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**


![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱 <<c0> [ 使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 和 SharePoint Online 的應用程式保護或應用程式架構原則

此案例包含存取 Exchange Online 和 SharePoint Online 的應用程式保護型 」 或 「 已核准的應用程式原則。


### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 設定用戶端應用程式，不是支援應用程式保護原則需求或已核准應用程式需求的應用程式清單上。  
- 使用符合應用程式保護原則需求，因此可接收 Intune 應用程式保護原則的用戶端應用程式。
- 使用符合已核准應用程式原則需求，支援 Intune 應用程式保護原則的用戶端應用程式。
- 開啟應用程式存取電子郵件或文件。
- 開啟 Outlook 應用程式，並使用 Azure AD 認證登入。
- 系統會提示安裝是用於 iOS 的 Microsoft Authenticator 或 Intune 公司入口網站，供 Android 使用，如果它們尚未安裝。
- 安裝應用程式，並可以返回 Outlook 應用程式，以繼續。
- 系統會提示註冊裝置。
- 可以接收 Intune 應用程式保護原則。
- 可以存取 Outlook 與 Intune 應用程式保護原則的電子郵件。
- 網站和文件不是在應用程式保護原則需求的應用程式可以存取，但已核准的應用程式需求中所列。

任何 Intune 應用程式保護原則所需的才能存取公司資料。 原則可能會提示使用者重新啟動應用程式，或使用其他 PIN。 如果原則已針對應用程式與平台，這會是大小寫。

**備註**

- 您可以使用這種情況下，如果您想要支援這兩個應用程式保護型和應用程式型條件式存取原則。
- 在此*或*存取已核准的用戶端應用程式需求之前，先評估原則，應用程式保護原則需求的應用程式。

### <a name="configuration"></a>組態

**步驟 1：適用於 Exchange Online 設定 Azure AD 條件式存取原則**

條件式存取原則，在此步驟中，設定下列元件：

![條件式存取](./media/app-protection-based-conditional-access/62.png)

1. 輸入您的條件式存取原則的名稱。

2. 底下**指派**，請在**使用者和群組**中，選取至少一個使用者或群組的每個條件式存取原則。

3. 在 **雲端應用程式**，選取**Office 365 Exchange Online**。 

     ![條件式存取](./media/app-protection-based-conditional-access/02.png)

4. 在 **條件**，設定**裝置平台**並**用戶端應用程式 （預覽）** 。 
 
    a. 在 **裝置平台**，選取**Android**並**iOS**。

    ![條件式存取](./media/app-protection-based-conditional-access/03.png)

    b. 在 **用戶端應用程式 （預覽）** ，選取**行動裝置應用程式和桌面用戶端**並**新式驗證用戶端**。

    ![條件式存取](./media/app-protection-based-conditional-access/91.png)

5. 底下**存取控制**，選取下列選項：

   - **需要核准的用戶端應用程式**

   - **需要應用程式保護原則 （預覽）**

   - [需要其中一個選取的控制項] 
 
     ![條件式存取](./media/app-protection-based-conditional-access/12.png)


**步驟 2：設定適用於 iOS 和 Android 用戶端應用程式的 Intune 應用程式保護原則**


![條件式存取](./media/app-protection-based-conditional-access/09.png)

如需詳細資訊，請參閱 <<c0> [ 使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。




## <a name="next-steps"></a>後續步驟

- 如果您想要了解如何設定條件式存取原則，請參閱[需要 MFA 的特定應用程式與 Azure Active Directory 條件式存取](app-based-mfa.md)。
- 如果您準備好設定您的環境的條件式存取原則，請參閱[Azure Active Directory 中的條件式存取的最佳作法](best-practices.md)。 