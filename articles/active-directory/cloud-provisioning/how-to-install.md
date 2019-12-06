---
title: 安裝 Azure AD Connect 雲端布建代理程式
description: 本主題說明如何安裝布建代理程式的逐步解說。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e3b2a113d46ff3d8799927f56fa66601c94ed5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846227"
---
# <a name="install-azure-ad-connect-cloud-provisioning-agent"></a>安裝 Azure AD Connect 雲端布建代理程式
本檔將逐步引導您完成 Azure AD Connect 布建代理程式的安裝程式，以及如何在 Azure 入口網站中一開始進行設定。

>[!IMPORTANT]
>下列安裝指示假設已符合所有[必要條件](how-to-prerequisites.md)。

安裝和設定 Azure AD Connect 布建會在下列步驟中完成：
    
- [安裝代理程式](#install-the-agent)
- [驗證代理程式安裝](#verify-agent-installation)


## <a name="install-the-agent"></a>安裝代理程式

1. 以企業系統管理員許可權登入您將使用的伺服器。
2. 流覽至 Azure 入口網站，選取左側的 [Azure Active Directory]。
3. 按一下 **[管理布建（預覽）** ]，然後選取 [**審查所有代理**程式]。
3. 從 Azure 入口網站下載 Azure AD Connect 布建代理程式。
![歡迎使用畫面](media/how-to-install/install9.png)</br>
3. 執行 Azure AD Connect 布建（AADConnectProvisioningAgent）
3. 在啟動顯示畫面上，**接受**授權條款，然後按一下 [**安裝**]。</br>
![歡迎使用畫面](media/how-to-install/install1.png)</br>

4. 此作業完成後，就會啟動設定 wizard。  使用您的 Azure AD 全域管理員帳戶登入。
5. 在 [連線**Active Directory]** 畫面上，按一下 [**新增目錄**]，然後使用您的 Active Directory 系統管理員帳戶登入。  此作業將會新增您的內部部署目錄。  按一下 [下一步]。</br>
![歡迎使用畫面](media/how-to-install/install3.png)</br>

6. 在 [設定**完成**] 畫面上，按一下 [**確認**]。  此作業將會註冊並重新啟動代理程式。</br>
![歡迎使用畫面](media/how-to-install/install4.png)</br>

7. 此作業完成後，您應該會看到已**成功驗證**的通知。  您**可以按一下 [** 結束]。</br>
![歡迎使用畫面](media/how-to-install/install5.png)</br>
8. 如果您仍然看到初始啟動顯示畫面，請按一下 [**關閉**]。


## <a name="verify-agent-installation"></a>驗證代理程式安裝
代理程式驗證會在 Azure 入口網站以及執行代理程式的本機伺服器上進行。

### <a name="azure-portal-agent-verification"></a>Azure 入口網站代理程式驗證
若要確認 Azure 正在查看代理程式，請遵循下列步驟：

1. 登入 Azure 入口網站。
2. 在左側選取 [ **Azure Active Directory**]，按一下 [ **Azure AD Connect** ]，然後在中央選取 [管理布建 **（預覽）** ]。</br>
![Azure 入口網站](media/how-to-install/install6.png)</br>

3.  在 [ **Azure AD 布建（預覽）** ] 畫面上，按一下 [**審查所有代理**程式]。
![Azure AD 布建](media/how-to-install/install7.png)</br>
 
4. 在 [**內部部署布建代理**程式] 畫面上，您會看到已安裝的代理程式。  確認有問題的代理程式存在，而且**已標示為使用中。**
![布建代理程式](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>驗證埠
若要確認 Azure 正在接聽埠443，且您的代理程式可以與其通訊，您可以使用下列方式：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此測試會驗證您的代理程式是否能夠透過埠443與 Azure 通訊。  開啟瀏覽器，並從安裝代理程式的伺服器流覽至上述 URL。
![服務](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本機伺服器上
若要確認代理程式是否正在執行，請遵循下列步驟：

1.  使用系統管理員帳戶登入伺服器
2.  藉由流覽至該**服務**或移至 [開始]/[執行中]/[services.msc] 來開啟它們。
3.  在 [**服務**] 下，確定**Microsoft Azure AD 連接代理程式更新程式**，並**Microsoft Azure AD connect 布建代理程式**存在且**狀態為 [** 執行中]。
![服務](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>代理程式已安裝，但必須先進行設定和啟用，才會開始同步處理使用者。  若要設定新的代理程式，請參閱 Azure AD Connect 布建[新的代理程式](how-to-configure.md)設定。



## <a name="next-steps"></a>後續步驟 

- [什麼是布建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端布建？](what-is-cloud-provisioning.md)
 
