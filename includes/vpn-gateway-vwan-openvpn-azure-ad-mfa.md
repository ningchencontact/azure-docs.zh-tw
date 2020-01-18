---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/16/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4cef4e09038288e4609f37c4e33f225bd79113be
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166699"
---
## <a name="mfa"></a>開啟 [MFA] 頁面

1. 登入 Azure 入口網站。
2. 流覽至**Azure Active Directory-> [所有使用者**]。
3. 選取 [**多重要素驗證**] 以開啟 [多重要素驗證] 頁面。

   ![登入](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>選取使用者

1. 在 [**多重要素驗證**] 頁面上，選取您想要啟用 MFA 的使用者。
2. 選取 [啟用]。

   ![選取](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>啟用驗證

1. 流覽至**Azure Active Directory-> 企業應用程式-> 所有應用程式**。
2. 在 [**企業應用程式-所有應用程式**] 頁面上，選取 [ **Azure VPN**]。

   ![目錄識別碼](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/user1.jpg)
