---
title: Azure Multi-Factor Auth Providers - Azure Active Directory
description: 何時應使用驗證提供者搭配 Azure MFA？
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b89f7416847e01cad8cb4f9bc52248d09170d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382011"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>何時使用 Azure Multi-Factor Authentication Provider

依預設，擁有 Azure Active Directory 和 Office 365 使用者的全域管理員可以使用雙步驟驗證。 不過，如果您想要充分利用[進階功能](howto-mfa-mfasettings.md)，則應該購買完整版的 Azure Multi-Factor Authentication (MFA)。

Azure Multi-Factor Auth Provider 可讓**沒有授權**的使用者能夠充分利用 Azure Multi-Factor Authentication 提供的功能。

> [!NOTE]
> 自 2018 年 9 月 1 日起，不再建立新的驗證提供者。 Existing auth providers may continue to be used and updated, but migration is no longer possible. 多重要素驗證將繼續為 Azure AD Premium 授權中的可用功能。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Azure MFA SDK 的相關注意事項

請注意，該 SDK 已遭取代，只會繼續運作到 2018 年 11 月 14 日為止。 在這個時間之後，對 SDK 的呼叫將會失敗。

## <a name="what-is-an-mfa-provider"></a>什麼是 MFA Provider？

驗證提供者的類型有兩種，差異在於您 Azure 訂用帳戶的收費方式。 每次驗證選項會計算一個月中對您的租用戶執行之驗證數目。 如果您有許多使用者偶爾才會進行驗證，最好使用此選項。 每位使用者選項會計算您的租用戶中一個月執行雙步驟驗證之個人數目。 如果您的某些使用者已擁有授權，但是需要將 MFA 擴充至您授權限制之外的更多使用者，最好使用此選項。

## <a name="manage-your-mfa-provider"></a>管理 MFA Provider

建立 MFA 提供者之後，您就無法變更使用量模型 (依據已啟用的使用者或依據驗證)。

如果您購買的授權已足夠讓啟用 MFA 的所有使用者使用，您可以一併刪除 MFA 提供者。

如果您的 MFA 提供者未連結至 Azure AD 租用戶，或是您將新的 MFA 提供者連結至不同 Azure AD 租用戶，則使用者設定和組態選項並不會進行轉移。 此外，現有 Azure MFA 伺服器也需要使用 MFA 提供者產生的啟用認證重新啟動。 重新啟用 MFA 伺服器以將其連結至 MFA 提供者，並不會影響電話和簡訊驗證，但所有使用者的行動裝置應用程式通知會停止運作，直到他們重新啟動行動裝置應用程式。

### <a name="removing-an-authentication-provider"></a>Removing an authentication provider

> [!CAUTION]
> There is no confirmation when deleting an authentication provider. Selecting **Delete** is a permanent process.

Authentication providers can be found in the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers**. Click on listed providers to see details and configurations associated with that provider.

Before removing an authentication provider, take note of any customized settings configured in your provider. Decide what settings need to be migrated to general MFA settings from your provider and complete the migration of those settings. 

Azure MFA Servers linked to providers will need to be reactivated using credentials generated under **Azure portal** > **Azure Active Directory** > **MFA** > **Server settings**. Before reactivating, the following files must be deleted from the `\Program Files\Multi-Factor Authentication Server\Data\` directory on Azure MFA Servers in your environment:

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Delete an auth provider from the Azure portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

When you have confirmed that all settings have been migrated, you can browse to the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers** and select the ellipses **...** and select **Delete**.

> [!WARNING]
> Deleting an authentication provider will delete any reporting information associated with that provider. You may want to save activity reports before deleting your provider.

> [!NOTE]
> Users with older versions of the Microsoft Authenticator app and Azure MFA Server may need to re-register their app.

## <a name="next-steps"></a>後續步驟

[進行 Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)
