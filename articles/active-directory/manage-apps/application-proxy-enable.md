---
title: Azure AD 應用程式 Proxy - 開始安裝連接器 | Microsoft Docs
description: 在 Azure 入口網站中開啟應用程式 Proxy，並安裝反向 Proxy 的連接器。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 683b5b24fe8e7da086e000ff38411d3eb1c2f781
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495738"
---
# <a name="get-started-with-application-proxy-and-install-the-connector"></a>開始使用應用程式 Proxy 並安裝連接器
此文章會逐步引導您在 Azure Active Directory (Azure AD) 中啟用應用程式 Proxy。

如果您還不知道應用程式 Proxy 為您的組織帶來的安全性和生產力優勢，請深入了解[如何為內部部署應用程式提供安全的遠端存取](application-proxy.md)。

## <a name="prerequisites"></a>必要條件
若要啟用應用程式 Proxy，您需要：

* [Microsoft Azure AD 基本或進階訂用帳戶](https://azure.microsoft.com/pricing/details/active-directory)。 
* 應用程式系統管理員帳戶。

### <a name="windows-server"></a>Windows 伺服器
您需要執行 Windows Server 2012 R2 或更新版本的伺服器，您可以在該伺服器上安裝「應用程式 Proxy」連接器。 伺服器需要連線至 Azure 中的「應用程式 Proxy」服務，以及您所發佈的內部部署應用程式。

您安裝「應用程式 Proxy」連接器之前，Windows 伺服器需要先啟用 TLS 1.2。 版本低於 1.5.612.0 的現有連接器可以繼續在先前的 TLS 版本上運作，直到進一步通知。 啟用 TLS 1.2：

1. 設定下列登錄機碼：
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. 重新啟動伺服器

對於使用 Kerberos 限制委派 (KCD) 的應用程式單一登入，Windows 伺服器和您所發佈的應用程式需要在相同的 Active Directory 網域中。 如需詳細資訊，請參閱[使用應用程式 Proxy 進行單一登入的 KCD](application-proxy-configure-single-sign-on-with-kcd.md)。
  
### <a name="proxy-servers"></a>Proxy 伺服器

如果您的組織使用 Proxy 伺服器連線到網際網路，您需要為它們設定應用程式 Proxy。  如需詳細資訊，請參閱[使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。 



## <a name="open-your-ports"></a>開啟您的連接埠

若要準備適合 Azure AD 應用程式 Proxy 的環境，您需要先啟用 Azure 資料中心的通訊。 如果路徑中有防火牆，請確定防火牆已開啟，以便連接器可以對應用程式 Proxy 提出 HTTPS (TCP) 要求。

1. 開啟下列連接埠以**輸出**流量：

   | 連接埠號碼 | 使用方式 |
   | --- | --- |
   | 80 | 下載憑證撤銷清單 (CRL) 時驗證 SSL 憑證 |
   | 443 | 應用程式 Proxy 服務的所有傳出通訊 |

   如果您的防火牆根據原始使用者強制執行流量，請針對來自當作網路服務執行之 Windows 服務的流量，開放這些連接埠。

   > [!IMPORTANT]
   > 下表反映連接器 1.5.132.0 版及更新版本的連接埠需求。 如果仍有舊版的連接器，您也需要啟用除了80 和 443 以外的下列連接埠：5671、8080、9090、9091、9350、9352、10100–10120。
   >
   >如需將連接器更新至最新版本的詳細資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md#automatic-updates)。

2. 如果您的防火牆或 Proxy 允許建立 DNS 允許清單，您可以建立 msappProxy.net 和 servicebus.windows.net 的允許清單連線。 如果不是，您需要允許對每週更新的 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)的存取。

3. Microsoft 使用四個位址來驗證憑證。 請允許存取下列 URL (若尚未允許其他產品存取)：
   * mscrl.microsoft.com:80
   * crl.microsoft.com:80
   * ocsp.msocsp.com:80
   * www.microsoft.com:80

4. 您的連接器必須存取 login.windows.net 和 login.microsoftonline.com 來進行註冊程序。


## <a name="install-and-register-a-connector"></a>安裝並註冊連接器
1. 在 [Azure 入口網站](https://portal.azure.com/)中，以系統管理員身分登入。
2. 您目前的目錄會顯示在右上角的使用者名稱之下。 如果您需要變更目錄，請選取該圖示。
3. 移至 [Azure Active Directory] > [應用程式 Proxy]。

   ![瀏覽至 [應用程式 Proxy]](./media/application-proxy-enable/app_proxy_navigate.png)

4. 選取 [下載連接器]。

   ![下載連接器](./media/application-proxy-enable/download_connector.png)

5. 在您根據必要條件所準備好的伺服器上執行 **AADApplicationProxyConnectorInstaller.exe** 。
6. 依照精靈中的指示進行安裝。 在安裝期間，系統將提示您向 Azure AD 租用戶的應用程式 Proxy 註冊連接器。

   * 提供您的 Azure AD 全域管理員認證。 您的全域管理員租用戶可能與您的 Microsoft Azure 認證不同。
   * 請確定註冊連接器的系統管理員與您啟用應用程式 Proxy 服務的位置在相同的目錄中。 例如，如果租用戶網域為 contoso.com，則系統管理員應該是 admin@contoso.com ，或該網域上的其他別名。
   * 如果 [IE 增強式安全性組態] 在您要安裝連接器的伺服器上設定為 [開啟]，您可能看不到註冊畫面。 若要取得存取權，請依照錯誤訊息中的指示。 請確定已停用 [Internet Explorer 增強式安全性]。

為了實現高可用性，您應該至少部署兩個連接器。 每個連接器都必須分別進行註冊。

## <a name="test-that-the-connector-installed-correctly"></a>測試連接器是否安裝正確

您可以在 Azure 入口網站中或在您的伺服器上檢查新的連接器，以確認它是否安裝正確。 

在 Azure 入口網站中，登入您的租用戶並巡覽至 [Azure Active Directory] > [應用程式 Proxy]。 所有的連接器與連接器群組都會出現在此頁面上。 選取連接器，以查看其詳細資料，或將它移至不同的連接器群組。 

在您的伺服器上，檢查連接器與連接器更新程式的作用中服務清單。 這兩項服務應該立即開始執行，但非如此，請加以啟動： 

   * **Microsoft AAD 應用程式 Proxy 連接器** 可啟用連線

   * **Microsoft AAD 應用程式 Proxy 連接器更新程式**是自動更新服務。 更新程式會檢查連接器的新版本，並且視需要更新連接器。

   ![應用程式 Proxy 連接器服務 - 螢幕擷取畫面](./media/application-proxy-enable/app_proxy_services.png)

如需有關連接器以及其如何保持最新狀態的詳細資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)。


## <a name="next-steps"></a>後續步驟
您現在已經準備好 [使用應用程式 Proxy 發佈應用程式](application-proxy-publish-azure-portal.md)。

如果您有位於不同網路或不同位置上的應用程式，請使用連接器群組將不同的連接器組織成邏輯單元。 深入了解 [使用應用程式 Proxy 連接器](application-proxy-connector-groups.md)。
