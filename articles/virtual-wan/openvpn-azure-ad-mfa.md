---
title: 為 VPN 使用者啟用 MFA： Azure AD 驗證
description: 為 VPN 使用者啟用多重要素驗證
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 9329ac33a771c829b8d67670bc07d9a353c58ff1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930585"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>為 VPN 使用者啟用 Azure 多重要素驗證（MFA）

如果您想要在授與存取權之前提示使用者提供第二個驗證因素，您可以為您的 Azure AD 租使用者設定 Azure 多重要素驗證（MFA）。 這篇文章中的步驟可協助您啟用雙步驟驗證的需求。

## <a name="prereq"></a>必要

這項設定的必要條件是使用[設定租](openvpn-azure-ad-tenant.md)使用者中的步驟設定 Azure AD 租使用者。

## <a name="mfa"></a>開啟 [MFA] 頁面

1. 登入 Azure 入口網站。
2. 流覽至**Azure Active Directory-> [所有使用者**]。
3. 選取 [**多重要素驗證**] 以開啟 [多重要素驗證] 頁面。

   ![登入](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>選取使用者

1. 在 [**多重要素驗證**] 頁面上，選取您想要啟用 MFA 的使用者。
2. 選取 [啟用]。

   ![選取](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>啟用驗證

1. 流覽至**Azure Active Directory-> 企業應用程式-> 所有應用程式**。
2. 在 [**企業應用程式-所有應用程式**] 頁面上，選取 [ **Azure VPN**]。

   ![目錄識別碼](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>設定登入設定

在 [ **AZURE VPN-屬性**] 頁面上，設定 [登入設定]。

1. 將 [**為使用者啟用登入嗎？** ] 設定為 **[是]** 。 這可讓 AD 租使用者中的所有使用者成功連接到 VPN。
2. **需要設定使用者指派嗎？** 如果您想要將登入限制為僅有 Azure VPN 許可權的使用者，則為 **[是]** 。
3. 儲存您的變更。

   ![使用權限](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[針對 Azure 的點對站連線設定 Azure AD 驗證](virtual-wan-point-to-site-azure-ad.md)。
