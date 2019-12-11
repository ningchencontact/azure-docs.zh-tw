---
title: Azure AD 中 Azure AD Connect 雲端布建的必要條件
description: 本主題說明雲端布建的先決條件和硬體需求。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f033563bbd7888e53d910773cd1e0c501eaad098
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997100"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect 雲端布建的必要條件
本主題提供有關選擇和使用 Azure AD Connect 雲端布建做為身分識別解決方案的指導方針。



## <a name="cloud-provisioning-agent-requirements"></a>雲端布建代理程式需求
您將需要下列內容，才能使用 Azure AD Connect 雲端布建：
    
- Azure AD 租使用者的全域系統管理員帳戶
- 適用于使用 Windows 2012 R2 或更新版本之布建代理程式的內部部署伺服器
- 內部部署防火牆設定

本檔的其餘部分將提供這些必要條件的逐步指示。

### <a name="in-the-azure-active-directory-admin-center"></a>於 Azure Active Directory 管理中心

1. 在 Azure AD 租用戶上建立僅限雲端的全域管理員帳戶。 如此一來，如果您的內部部署服務失敗或無法使用，您便可以管理租用戶組態。 了解如何[新增僅限雲端管理員帳戶](../active-directory-users-create-azure-portal.md)。 這是確保您不會遭租用戶封鎖的關鍵步驟。
2. 將一或多個[自訂網域名稱](../active-directory-domains-add-azure-portal.md)新增至 Azure AD 租用戶。 您的使用者可以使用其中一個網域名稱登入。

### <a name="in-your-on-premises-environment"></a>在內部部署環境中

1. 識別執行 Windows Server 2012 R2 或更新版本的已加入網域主機伺服器，最少 4 GB RAM 和 .NET 4.7.1 + 執行時間 

2. 如果您的伺服器和 Azure AD 之間有防火牆，請設定下列項目：
   - 請確定代理程式可以透過下列埠對 Azure AD 提出*輸出*要求：

     | 連接埠號碼 | 使用方式 |
     | --- | --- |
     | **80** | 驗證 SSL 憑證時下載憑證撤銷清單 (CRL) |
     | **443** | 處理所有與服務之間的輸出通訊 |
     | **8080** (選擇性) | 如果埠443無法使用，代理程式會每隔十分鐘透過埠8080報告其狀態。 此狀態會顯示在 Azure 入口網站上。 |
     
     如果您的防火牆會根據原始使用者強制執行規則，請開啟這些連接埠，讓來自以網路服務形式執行之 Windows 服務的流量得以通行。
   - 如果您的防火牆或 proxy 允許您指定安全尾碼，請將連接新增至 **\*msappproxy.net**和 **\*. servicebus.windows.net**。 如果不允許建立，請允許存取每週更新的 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
   - 您的代理程式需要存取**login.windows.net**和**login.microsoftonline.com** ，才能進行初始註冊。 因此也請針對這些 URL 開啟您的防火牆。
   - 針對憑證驗證，請解除封鎖下列 Url： **mscrl.microsoft.com:80**、 **crl.microsoft.com:80**、 **ocsp.msocsp.com:80**和**www\.microsoft.com:80**。 由於這些 URL 會用於其他 Microsoft 產品的憑證驗證，因此您可能已將這些 URL 解除封鎖。

### <a name="verify-the-port"></a>驗證埠
若要確認 Azure 正在接聽埠443，且您的代理程式可以與其通訊，您可以使用下列方式：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此測試會驗證您的代理程式是否能夠透過埠443與 Azure 通訊。  開啟瀏覽器，並從安裝代理程式的伺服器流覽至上述 URL。
![服務](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>其他需求
- [Microsoft .NET Framework 4.7。1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 需求

>[!NOTE]
>傳輸層安全性（TLS）是提供安全通訊的通訊協定。  變更 TLS 設定會影響整個樹系。  如需詳細資訊，請參閱[更新以啟用 TLS 1.1 和 tls 1.2 做為 Windows 中 WinHTTP 中的預設安全通訊協定](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

將裝載 Azure AD Connect 雲端布建代理程式的 Windows server 必須先啟用 TLS 1.2，才能進行安裝。

啟用 TLS 1.2：

1. 設定下列登錄機碼：
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. 重新啟動伺服器。


## <a name="next-steps"></a>後續步驟 

- [什麼是布建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端布建？](what-is-cloud-provisioning.md)

