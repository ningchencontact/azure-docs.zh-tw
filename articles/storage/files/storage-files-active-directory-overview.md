---
title: 針對 Azure 檔案透過 SMB 進行 Azure Active Directory 驗證 (預覽) 的概觀 - Azure 儲存體
description: Azure 檔案支援透過 Azure Active Directory (Azure AD) Domain Services 在 SMB (伺服器訊息區) 上進行以身分識別為基礎的驗證 (預覽)。 然後，您加入網域的 Windows 虛擬機器 (VM) 可以使用 Azure AD 認證存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: b1bc7385751fbd1829b4aee2713621448f8aa505
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699726"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>瞭解 SMB 存取的 Azure 檔案儲存體 Azure Active Directory 網域服務 (AAD DS) 驗證支援 (預覽)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

若要瞭解如何啟用 Azure 檔案儲存體的 AAD DS 驗證, 請參閱[針對 Azure 檔案儲存體 (預覽) 啟用透過 SMB 的 Azure Active Directory 網域服務驗證](storage-files-active-directory-enable.md)。

## <a name="glossary"></a>字彙 
請務必瞭解一些與 SMB 上的 Azure AD 網域服務驗證有關 Azure 檔案儲存體的重要詞彙:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) 是 Microsoft 的多重租用戶雲端式目錄和身分識別管理服務。 Azure AD 將核心目錄服務、應用程式存取管理及身分識別保護結合到單個解決方案。 如需詳細資訊，請參閱[什麼是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD 網域服務**  
    Azure AD Domain Services 提供受控網域服務，例如：加入網域、群組原則、LDAP 以及 Kerberos/NTLM 驗證。 這些服候與 Windows Server Active Directory 完全相容。 如需詳細資訊，請參閱 [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)。

-   **Azure 角色型存取控制 (RBAC)**  
    Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。 使用 RBAC，您可以對使用者授權執行其執行工作所需的最少權限，以便管理對資源的存取權。 如需 RBAC 的詳細資訊，請參閱[什麼是 Azure 中的角色型存取控制 (RBAC)？](../../role-based-access-control/overview.md)

-   **Kerberos 驗證**

    Kerberos 是用來驗證的使用者或主機身分識別的驗證通訊協定。 如需有關 Kerberos 的詳細資訊，請參閱 [Kerberos 驗證概觀](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)。

-  **伺服器訊息區 (SMB) 通訊協定**  
    SMB 是業界標準網路檔案共用通訊協定。 SMB 亦稱為 Common Internet File System 或 CIFS。 如需有關 SMB 的詳細資訊，請參閱 [Microsoft SMB 通訊協定和 CIFS 通訊協定概觀](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)。

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Azure AD 網域服務驗證的優點
Azure 檔案儲存體的 Azure AD 網域服務驗證提供了使用共用金鑰驗證的幾個優點:

-   **使用 Azure AD 和 Azure AD 網域服務, 將傳統身分識別型檔案共用存取體驗延伸到雲端**  
    如果您打算將應用程式「隨即轉移」到雲端, 以 Azure 檔案儲存體取代傳統的檔案伺服器, 則您可能會想要讓應用程式使用 Azure AD 認證進行驗證, 以存取檔案資料。 Azure 檔案儲存體支援使用 Azure AD 認證, 從已加入 AAD DS 網域的 Windows Vm 存取 SMB 的 Azure 檔案儲存體。 您也可以選擇將所有的內部部署 Active Directory 物件同步到 Azure AD，以保留使用者名稱、密碼及其他群組指派。

-   **在 Azure 檔案共用上強制執行細微存取控制**  
    您可以在共用、目錄或檔案層級, 將許可權授與特定的身分識別。 例如，假設您有多個小組使用同一個 Azure 檔案共用來共同作業某個專案。 您可以授權所有小組存取非敏感目錄，同時將包含敏感財務資料之目錄的存取權僅授與財務小組。 

-   **一起備份 ACL 和資料**  
    您可以使用 Azure 檔案來備份現有的內部部署檔案共用。 透過 SMB 將檔案共用備份到 Azure 檔案時，Azure 檔案會一起保存您的 ACL 和資料。

## <a name="how-it-works"></a>運作方式
Azure 檔案使用 Azure AD Domain Services 來支援從加入網域的 VM 搭配 Azure AD 認證進行 Kerberos 驗證。 您可以使用 Azure AD 與 Azure 檔案之前，必須先啟用 Azure AD Domain Services，並加入打算存取其檔案資料的 VM 網域。 已加入網域的 VM 必須位於與 Azure AD Domain Services 相同的虛擬網路 (VNET) 中。 

當與 VM 上執行應用程式相關聯的身分識別嘗試存取 Azure 檔案中的資料時，會傳送一個要求給 Azure AD Domain Services 來驗證身分識別。 如果驗證成功，Azure AD Domain Services 就會傳回 Kerberos 權杖。 應用程式傳送一個包含 Kerberos 權杖的要求，Azure 檔案則使用該權杖來授權 要求。 Azure 檔案只接受權杖，並不會保存 Azure AD 認證。

![顯示「透過 SMB 的 Azure AD 驗證」圖的螢幕擷取畫面](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>啟用 Azure AD 網域服務驗證以進行 SMB 存取
您可以在2018年9月24日之後建立之新的和現有的儲存體帳戶上, 啟用 Azure AD 網域服務驗證以進行 Azure 檔案儲存體。 

啟用此功能之前, 請確認已針對與您的儲存體帳戶相關聯的主要 Azure AD 租使用者部署 Azure AD Domain Services。 如果您尚未設定 Azure AD Domain Services，請遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/create-instance.md) 中的逐步指示操作。

Azure AD Domain Services 部署通常需要 10 到 15 分鐘。 在部署 Azure AD Domain Services 之後，您可以為 Azure 檔案啟用透過 SMB 進行 Azure AD 驗證的功能。 如需詳細資訊, 請參閱[針對 Azure 檔案儲存體啟用透過 SMB Azure Active Directory 網域服務驗證 (預覽)](storage-files-active-directory-enable.md)。 

### <a name="configure-share-level-permissions-for-azure-files"></a>設定 Azure 檔案共用層級權限
一旦啟用 Azure AD 網域服務驗證之後, 您就可以設定 Azure AD 身分識別的自訂 RBAC 角色, 並將存取權限指派給儲存體帳戶中的任何檔案共用。

當已加入網域的 VM 上執行的應用程式嘗試掛接 Azure 檔案共用或存取目錄或檔案時，系統會驗證應用程式的 Azure AD 認證以確認正確的共用層級權限和 NTFS 權限。 如需設定共用層級許可權的詳細資訊, 請參閱[啟用透過 SMB 的 Azure Active Directory 網域服務驗證 (預覽)](storage-files-active-directory-enable.md)。

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>設定 Azure 檔案的目錄層級或檔案層級的權限 
Azure 檔案在目錄和檔案層級 (包括根目錄) 強制執行標準 NTFS 檔案權限。 目錄或檔案層級權限僅支援透過 SMB 進行設定。 使用 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 或 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 命令從 VM 掛接目標檔案共用和設定權限。 

> [!NOTE]
> 預覽版不支援透過 Windows 檔案總管設定 NTFS 權限。

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>為超級使用者權限使用儲存體帳戶金鑰 
擁有儲存體帳戶金鑰的使用者可以使用超級使用者權限存取 Azure 檔案。 超級使用者權限大於使用 RBAC 在共用層級設定的限制以及 Azure AD 強制執行的限制。 若要掛接 Azure 檔案共用，需要超級使用者權限。 

> [!IMPORTANT]
> 最佳安全做法是避免共用儲存體帳戶金鑰並盡可能利用 Azure AD 權限。

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>資料匯入至 Azure 檔案共用時保存目錄和檔案 ACL
當您將資料複製到 Azure 檔案共用時, Azure 檔案儲存體現在支援保留目錄或檔案 Acl。 您可以將目錄或檔案上的 Acl 複製到 Azure 檔案儲存體。 例如，您可以使用 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 加旗標`/copy:s`，將資料和 ACL 兩者一起複製到 Azure 檔案共用。 ACL 保留預設為開啟, 您不需要在儲存體帳戶上明確啟用 Azure AD 網域服務驗證功能。 

## <a name="pricing"></a>定價
在儲存體帳戶上啟用「透過 SMB 進行 Azure AD 驗證」不需要額外付費。 如需有關定價的詳細資訊，請參閱 [Azure 檔案定價](https://azure.microsoft.com/pricing/details/storage/files/)和 [Azure AD Domain Services 定價](https://azure.microsoft.com/pricing/details/active-directory-ds/)頁面。

## <a name="next-steps"></a>後續步驟
如需 Azure 檔案以及使用「透過 SMB 進行 Azure AD 驗證」的詳細資訊，請參閱下列資源：

- [Azure 檔案服務簡介](storage-files-introduction.md)
- [為 Azure 檔案啟用「透過 SMB 進行 Azure Active Directory 驗證」 (預覽)](storage-files-active-directory-enable.md)
- [常見問題集](storage-files-faq.md)
