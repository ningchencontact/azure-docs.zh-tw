---
title: "Azure Active Directory 網域服務的 Azure 雲端方案提供者 |Microsoft 文件"
description: "Azure Active Directory 網域服務的 Azure 雲端方案提供者。"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 313c4853b0f585921739779bb764c50036c9ac8a
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2017
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure 雲端方案提供者 (CSP) 的 azure Active Directory (AD) 網域服務
本文說明如何使用 Azure AD 網域服務中的 CSP Azure 訂用帳戶。

## <a name="overview-of-azure-csp"></a>Azure 的 CSP 的概觀
Azure 的 CSP 是適用於 Microsoft 合作夥伴的程式，並提供各種 Microsoft 雲端服務授權通路。 Azure 的 CSP 可讓合作夥伴管理銷售、 建立計費關係、 提供技術與帳務支援，而且為客戶的單一窗口。 此外，Azure CSP 會提供一組完整的工具，包括自助服務入口網站和隨附的應用程式開發介面。 這些工具使 CSP 合作夥伴能夠輕鬆地佈建及管理 Azure 資源，以及提供客戶和其訂用帳戶的帳單。

[合作夥伴中心入口網站](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)做為進入點，讓所有 Azure CSP 的夥伴。 它提供豐富客戶管理功能、 自動化的處理，等等。 Azure 的 CSP 合作夥伴可以使用合作夥伴中心功能，使用網頁型的 UI，或使用 PowerShell 和各種 API 呼叫。

下圖說明 CSP 模型以高層級的運作方式。 Contoso 有 Azure AD Active Directory。 它們有合作關係與 CSP 可以部署和管理其 Azure CSP 訂用帳戶中的資源。 Contoso 也可能需要定期 （直接） Azure 訂用帳戶，這會直接對 Contoso 計費。

![CSP 模型概觀](./media/csp/csp_model_overview.png)

CSP 夥伴的租用戶都有三個特殊的代理程式群組的管理代理程式 」、 「 技術服務人員代理程式和 「 銷售代理程式。 管理代理程式群組指派給 Contoso 的 Azure AD 目錄中的租用戶系統管理員角色。 如此一來，屬於 CSP 夥伴的管理代理程式群組的使用者會在 Contoso 的 Azure AD 目錄中有租用戶系統管理員權限。 當 CSP 夥伴佈建 Azure CSP 訂用帳戶，為了讓 Contoso，其管理代理程式群組指派給該訂用帳戶擁有者角色。 如此一來，CSP 夥伴的管理代理程式發生佈建 Azure 資源，例如虛擬機器、 虛擬網路以及 Azure AD 網域服務，代表 Contoso 的必要權限。

如需詳細資訊，請參閱[Azure CSP 概觀](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Azure CSP 訂用帳戶中使用 Azure AD 網域服務的優點
Azure AD 網域服務提供 Windows Server AD 相容的服務，例如 LDAP、 Kerberos/NTLM 驗證、 加入網域，群組原則和 DNS 的 Azure 中。 透過數十年，許多應用程式已經建置工作針對 AD 使用這些功能。 許多的獨立軟體廠商 (Isv) 建置與部署在其客戶的內部部署的應用程式。 這些應用程式是繁重支援，因為通常需要不同的環境中部署這些應用程式的存取權。 Azure CSP 訂用帳戶，您可以調整與 Azure 的彈性簡單的替代方案。

Azure AD 網域服務現在支援 Azure CSP 訂用帳戶。 您現在可以部署應用程式中繫結至客戶的 Azure AD 目錄的 CSP Azure 訂用帳戶。 如此一來，您的員工 （支援人員） 可以管理、 管理和服務在其部署應用程式使用貴組織的公司認證的虛擬機器。 此外，您可以佈建客戶的 Azure AD 目錄的 Azure AD 網域服務受管理的網域。 您的應用程式會連接到您的客戶受管理的網域。 因此，應用程式中的功能依賴 Kerberos/NTLM，LDAP，或[System.DirectoryServices API](https://msdn.microsoft.com/library/system.directoryservices)運作順暢地對客戶的目錄。 結束客戶受益取用您的應用程式，為服務，而不需要擔心維護應用程式部署的基礎。

所有計費的 Azure 資源，您可以使用，因為訂用帳戶，包括 Azure AD 網域服務會負責回給您。 談到銷售、 計費、 技術支援等，您可以維護與客戶的關聯性的完整控制權。Azure CSP 平台的彈性，小型的小組，支援的代理程式可以服務許多這類客戶都有執行個體的應用程式部署。


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Azure AD 網域服務的 CSP 部署模型
有兩種方式，讓您可以使用 Azure AD 網域服務與 Azure CSP 訂用帳戶。 挑選適合您的客戶的安全性和簡單性考量為基礎。

### <a name="direct-deployment-model"></a>直接部署模型
在這種部署模型，Azure AD 網域服務被啟用虛擬網路屬於 Azure CSP 訂用帳戶中。 CSP 夥伴的管理代理程式擁有下列權限：
* 客戶的 Azure AD 目錄中的全域系統管理員權限。
* 訂用帳戶的 Azure CSP 訂用帳戶的擁有者權限。

![直接部署模型](./media/csp/csp_direct_deployment_model.png)

在這種部署模型，CSP 提供者的管理代理程式可以將身分識別管理的客戶。 這些管理代理程式有提供新的使用者和群組、 新增應用程式內客戶的 Azure AD 目錄等的能力。這種部署模型可能適合較小的組織不具有專用的身分識別管理員或偏好的 CSP 合作夥伴來管理代表他們的身分識別。


### <a name="peered-deployment-model"></a>所以部署模型
在這種部署模型，屬於客戶-也就是直接 Azure 訂用帳戶支付客戶虛擬網路中啟用 Azure AD 網域服務。 CSP 夥伴然後才能部署虛擬網路屬於客戶的 CSP 訂用帳戶中的應用程式。 虛擬網路然後可以使用 Azure 虛擬網路對等互連來連接。 如此一來，由 Azure CSP 訂用帳戶中的 CSP 夥伴部署工作負載/應用程式可以連接到的客戶在客戶的直接的 Azure 訂用帳戶中佈建受管理的網域。

![所以部署模型](./media/csp/csp_peered_deployment_model.png)

這種部署模型提供隔離的權限，並讓管理 Azure 訂用帳戶和部署和管理資源內的 CSP 夥伴的技術服務人員代理程式。 不過，CSP 夥伴的技術服務人員代理程式不需要對客戶的 Azure AD 目錄全域系統管理員權限。 客戶的身分識別的系統管理員可以繼續管理其組織的身分識別。

這種部署模型可能適合 ISV （獨立軟體廠商），提供裝載的案例也需要連線到客戶其內部部署應用程式版本的 AD。


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>管理 Azure AD 網域服務管理 CSP 訂用帳戶中的網域
管理受管理的網域中的 CSP Azure 訂用帳戶時，適用於下列重要事項：

* **CSP 管理代理程式可以佈建受管理的網域使用其認證：** Azure AD 網域服務可支援 Azure CSP 訂閱。 因此，屬於 CSP 協力廠商的管理代理程式群組的使用者可以提供新的 Azure AD 網域服務受管理的網域。

* **Csp 可編寫指令碼為客戶使用 PowerShell 建立新受管理的網域：**看到[如何啟用 Azure AD 網域服務以使用 PowerShell](active-directory-ds-enable-using-powershell.md)如需詳細資訊。

* **CSP 管理代理程式無法在受管理的網域使用其認證執行進行中的管理工作：** CSP admin 使用者無法執行使用其認證的受管理網域內的例行管理工作。 這些使用者將會在客戶的 Azure AD 目錄的外部或其認證不能使用客戶的 Azure AD 目錄中。 因此，Azure AD 網域服務並沒有這些使用者的存取權的 Kerberos and NTLM 密碼雜湊。 如此一來，這類使用者無法在受管理的 Azure AD 網域服務網域上進行驗證。

  > [!WARNING]
  > **您必須建立在受管理的網域上執行進行中的管理工作的客戶的目錄中的使用者帳戶。**
  > 您無法登入受管理的網域使用 CSP 系統管理使用者的認證。 使用屬於客戶的 Azure AD 目錄的使用者帳戶的認證，若要這樣做。 您需要這些認證，例如將虛擬機器加入受管理的網域、 DNS 的管理、 管理群組原則等工作。
  >

* **建立進行中的管理必須加入至 'AAD DC Administrators' 群組的使用者帳戶：** ' AAD DC Administrators' 群組的受管理的網域上執行特定的委派的系統管理工作的權限。 這些工作包括設定 DNS，建立組織單位，管理 群組原則等。在受管理的網域上執行這類工作 CSP 合作夥伴，使用者帳戶需要客戶的 Azure AD 目錄中建立。 此帳戶的認證必須與 CSP 夥伴的管理代理程式共用。 此外，此使用者帳戶必須加入 AAD DC Administrators' 群組，以啟用要使用此使用者帳戶來執行受管理的網域上的組態工作。


## <a name="next-steps"></a>後續步驟
* [註冊 Azure CSP 計畫](https://partnercenter.microsoft.com/partner/programs)，然後開始建立透過 Azure CSP 的企業。
* 檢閱清單[Azure CSP 中可用的 Azure 服務](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。
* [使用 PowerShell 啟用 Azure AD Domain Services](active-directory-ds-enable-using-powershell.md)
* [開始使用 Azure AD Domain Services](active-directory-ds-getting-started.md)
