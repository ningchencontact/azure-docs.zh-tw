---
title: 為 VPN 使用者啟用 MFA： Azure AD authentication |Microsoft Docs
description: 為 VPN 使用者啟用多重要素驗證
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: alzam
ms.openlocfilehash: 3f407ecfcbfdff72b79bfe6b5bc3e6b7606b1cf0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174625"
---
# <a name="enable-multi-factor-authentication-mfa-for-vpn-users"></a>為 VPN 使用者啟用多重要素驗證（MFA）

您可以為 Azure AD 租使用者中的使用者啟用 MFA，以便在授與存取權之前，先提示使用者進行第二因素驗證。

> [!必要條件] 您已依照「設定租使用者」檔中所述的方式設定 Azure AD 租使用者。
>

### <a name="tenant"></a>1. 登入 Azure 入口網站並流覽至 [ **Azure Active Directory** ]、[**所有使用者**]，然後按一下 [**多重要素驗證**]


   ![新增 Azure AD 租使用者](./media/openvpn-azure-ad-mfa/mfa1.jpg)

### <a name="users"></a>2. 選取您要啟用 MFA 的使用者，然後按一下 [**啟用**]

   ![新增 Azure AD 租使用者](./media/openvpn-azure-ad-mfa/mfa2.jpg)

### <a name="enable-authentication"></a>3. 流覽至 [ **Azure Active Directory** ]、[**企業應用程式**]、[**所有應用程式**]，然後按一下**Azure VPN**


   ![目錄識別碼](./media/openvpn-azure-ad-mfa/user1.jpg)

### <a name="users"></a>4. 確定 [**已啟用使用者登入嗎？** ] 已設定為 [是]。 如果您想要讓具有 Azure VPN 許可權的使用者能夠登入，然後設定 [**需要使用者指派？** ] 設為 [是]，否則 AD 租使用者中的所有使用者都能夠成功連線到 VPN。

   ![權限](./media/openvpn-azure-ad-mfa/user2.jpg)


## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[設定 vpn 用戶端以進行 P2S vpn](openvpn-azure-ad-client.md)連線。
