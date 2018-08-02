---
title: 何時及如何使用 Azure Multi-Factor Auth Provider？
description: 何時應使用驗證提供者搭配 Azure MFA？
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 4a6ce07bfe641d9efdbe0eac841bb4f27f468b34
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161459"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>何時使用 Azure Multi-Factor Authentication Provider

依預設，擁有 Azure Active Directory 和 Office 365 使用者的全域管理員可以使用雙步驟驗證。 不過，如果您想要充分利用[進階功能](howto-mfa-mfasettings.md)，則應該購買完整版的 Azure Multi-Factor Authentication (MFA)。

Azure Multi-Factor Auth Provider 可用來充分利用完整版 Azure MFA 所提供的功能。 它的適用對象是**未透過 Azure MFA、Azure AD Premium 或包含 Azure AD Premium 或 Azure MFA 的搭售方案取得授權**的使用者。 Azure MFA 和 Azure AD Premium 預設會包含完整版 Azure MFA。

如果您有涵蓋組織中所有使用者的授權，則不需要 Azure Multi-Factor Auth Provider。 只有在也需要為一些沒有授權的使用者提供雙步驟驗證時，才需建立 Azure Multi-Factor Authentication 提供者。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Azure MFA SDK 的相關注意事項

下載 SDK 需要 Azure Multi-Factor Auth Provider。 請注意 SDK 已被取代，不再對新客戶提供支援，而且只會繼續運作到 2018 年 11 月 14 日為止。 在這個時間之後，對 SDK 的呼叫將會失敗。

若要下載 SDK，即使您有 Azure MFA、AAD Premium 或其他配套授權，還是必須建立 Azure Multi-Factor Auth Provider。 如果您針對此用途建立 Azure Multi-Factor Auth Provider，且已有授權，請務必使用**每個啟用的使用者**模型建立提供者。 然後，將提供者連結至包含 Azure MFA、Azure AD Premium 或其他配套授權的目錄。 這個組態可確保您只會在執行雙步驟驗證的唯一使用者超過您所擁有的授權數目時收到帳單。

## <a name="what-is-an-mfa-provider"></a>什麼是 MFA Provider？

如果您沒有 Azure Multi-factor Authentication 的授權，可以建立驗證提供者，為您的使用者要求雙步驟驗證。

驗證提供者的類型有兩種，差異在於您 Azure 訂用帳戶的收費方式。 每次驗證選項會計算一個月中對您的租用戶執行之驗證數目。 如果您有許多使用者偶爾才會進行驗證，最好使用此選項。 每位使用者選項會計算您的租用戶中一個月執行雙步驟驗證之個人數目。 如果您的某些使用者已擁有授權，但是需要將 MFA 擴充至您授權限制之外的更多使用者，最好使用此選項。

## <a name="create-an-mfa-provider"></a>建立 MFA Provider

使用下列步驟，在 Azure 入口網站中建立 Azure Multi-Factor Authentication Provider：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [Azure Active Directory] > [MFA Server] > [提供者]。

   ![提供者][Providers]

3. 選取 [新增] 。
4. 填寫下列欄位，然後選取 [新增]：
   - **名稱** – 提供者的名稱。
   - **使用量模型** – 選擇兩個選項其中之一：
      * 每次驗證 – 購買依每次驗證付費的模式。 通常用於在取用者導向應用程式中使用 Azure Multi-factor Authentication 的案例。
      * 每個啟用的使用者 – 購買依每個啟用使用者付費的模式。 通常用於員工存取 Office 365 之類的應用程式。 如果某些使用者已擁有 Azure MFA 的授權，請選擇這個選項。
   - **訂用帳戶** – 透過 Provider 針對雙步驟驗證活動計費的 Azure 訂用帳戶。
   - **目錄** – 與 Provider 相關聯的 Azure Active Directory 租用戶。
      * 您不需要 Azure AD 目錄即可建立 Provider。 如果您只打算下載 Azure Multi-Factor Authentication Server，請將方塊保留空白。
      * Provider 必須與 Azure AD 目錄產生關聯，才能利用進階功能。
      * 只有一個 Provider 可以與任何一個 Azure AD 目錄相關聯。

## <a name="manage-your-mfa-provider"></a>管理 MFA Provider

建立 MFA 提供者之後，您就無法變更使用量模型 (依據已啟用的使用者或依據驗證)。 不過，您可以刪除 MFA 提供者，然後建立一個使用不同使用量模型的 MFA 提供者。

如果目前的 Multi-Factor Auth Provider 與 Azure AD 目錄 (也稱為 Azure AD 租用戶) 相關聯，您就可以安全地將 MFA 提供者刪除，並建立連結至相同 Azure AD 租用戶的 MFA 提供者。 或者，如果您購買的 MFA、Azure AD Premium 或包含 Azure AD Premium 或 Azure MFA 授權的搭售方案已足夠讓啟用 MFA 的所有使用者使用，您可以一併刪除 MFA 提供者。

如果您的 MFA 提供者未連結至 Azure AD 租用戶，或是您將新的 MFA 提供者連結至不同 Azure AD 租用戶，則使用者設定和組態選項並不會進行轉移。 此外，現有 Azure MFA 伺服器也需要使用新 MFA 提供者產生的啟用認證重新啟動。 重新啟用 MFA 伺服器以將其連結至新的 MFA 提供者，並不會影響電話和簡訊驗證，但所有使用者的行動裝置應用程式通知會停止運作，直到他們重新啟動行動裝置應用程式。

## <a name="next-steps"></a>後續步驟

[進行 Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "新增 MFA 提供者"
