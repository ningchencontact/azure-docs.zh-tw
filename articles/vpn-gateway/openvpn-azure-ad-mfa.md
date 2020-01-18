---
title: 為 VPN 使用者啟用 MFA： Azure AD 驗證
description: 為 VPN 使用者啟用多重要素驗證
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: b22581d012b2c69081bc7b4eee093227c060b4c2
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169702"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>為 VPN 使用者啟用 Azure 多重要素驗證（MFA）

如果您想要在授與存取權之前提示使用者提供第二個驗證因素，您可以為您的 Azure AD 租使用者設定 Azure 多重要素驗證（MFA）。 這篇文章中的步驟可協助您啟用雙步驟驗證的需求。

## <a name="prereq"></a>必要

這項設定的必要條件是使用[設定租](openvpn-azure-ad-tenant.md)使用者中的步驟設定 Azure AD 租使用者。

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>設定登入設定

在 [ **AZURE VPN-屬性**] 頁面上，設定 [登入設定]。

1. 將 [**為使用者啟用登入嗎？** ] 設定為 **[是]** 。 這可讓 AD 租使用者中的所有使用者成功連接到 VPN。
2. **需要設定使用者指派嗎？** 如果您想要將登入限制為僅有 Azure VPN 許可權的使用者，則為 **[是]** 。
3. 儲存您的變更。

   ![使用權限](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[設定 vpn 用戶端以進行 P2S vpn](openvpn-azure-ad-client.md)連線。
