---
title: 為 Azure 檔案 (預覽) 啟用「透過 SMB 進行 Azure Active Directory 驗證」- Azure 儲存體
description: 了解如何透過 Azure Active Directory (Azure AD) Domain Services，為 Azure 檔案啟用「透過 SMB (伺服器訊息區) (預覽) 進行以身分識別為基礎的驗證」。 然後，您加入網域的 Windows 虛擬機器 (VM) 可以使用 Azure AD 認證存取 Azure 檔案共用。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696130"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>透過 SMB 中啟用 Azure Active Directory 網域服務驗證，Azure 檔案 （預覽）
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

如需了解為 Azure 檔案使用「透過 SMB 進行 Azure AD 驗證」的概觀，請參閱[為 Azure 檔案 (預覽) 使用「透過 SMB 進行 Azure Active Directory 驗證」的概觀](storage-files-active-directory-overview.md)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>工作流程概觀
SMB 上啟用 Azure AD DS 驗證適用於 Azure 檔案之前，請確認您的 Azure AD 和 Azure 儲存體環境已正確設定。 建議您逐步進行[必要條件](#prerequisites)，以確定您已執行所有必要步驟。 

接著，遵循下列步驟，授與對 Azure AD 認證之 Azure 檔案資源的存取權： 

1. 透過 SMB 儲存體帳戶向儲存體帳戶相關聯的 Azure AD Domain Services 部署中啟用 Azure AD DS 的驗證。
2. 將共用的存取權限指派至 Azure AD 身分識別 (使用者、群組或服務主體)。
3. 透過 SMB 為目錄和檔案設定 NTFS 權限。
4. 從已加入網域的虛擬機器中裝載 Azure 檔案共用。

下圖說明透過 SMB 中啟用 Azure AD DS 驗證，Azure 檔案的端對端工作流程。 

![此圖顯示針對 Azure 檔案使用「透過 SMB 進行 Azure AD 驗證」的工作流程](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>先決條件 

針對 Azure 檔案服務啟用透過 SMB 進行 Azure AD 驗證之前，請確定您已完成下列必要條件：

1.  **選取或建立 Azure AD 租用戶。**

    您可以針對「透過 SMB 進行 Azure AD 驗證」使用全新或現有租用戶。 租用戶和您想要存取的檔案共用必須與相同的訂用帳戶相關聯。

    若要建立新的 Azure AD 租用戶，您可以[新增 Azure AD 租用戶和 Azure AD 訂用帳戶](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果您有現有的 Azure AD 租用戶，但想要建立新的租用戶與 Azure 檔案搭配使用，請參閱[建立 Azure Active Directory 租用戶](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

2.  **在 Azure AD 租用戶上啟用 Azure AD Domain Services。**

    若要支援使用 Azure AD 認證進行驗證，您必須為 Azure AD 租用戶啟用 Azure AD Domain Services。 如果您不是 Azure AD 租用戶的系統管理員，請連絡系統管理員，並遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/create-instance.md) 的逐步指導。

    通常需要大約 15 分鐘才能完成 Azure AD Domain Services 部署。 請先確認 Azure AD Domain Services 的健康情況顯示**執行中**，並已啟用密碼雜湊同步，才能繼續進行下一個步驟。

3.  **加入網域的 Azure VM 具有 Azure AD Domain Services。**

    若要使用虛擬機器的 Azure AD 認證存取檔案共用，您的虛擬機器網域必須加入至 Azure AD Domain Services。 如需如何加入虛擬機器網域的詳細資訊，請參閱[將 Windows Server 虛擬機器加入受控網域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > 透過 SMB 使用 Azure 檔案的 azure AD DS 驗證只支援 Windows 7 或 Windows Server 2008 R2 以上作業系統版本上執行的 Azure Vm。

4.  **選取或建立 Azure 檔案共用。**

    選取與 Azure AD 租用戶相同訂閱關聯的全新或現有檔案共用。 如需建立新檔案共用的資訊，請參閱 [在 Azure 檔案中建立檔案共用](storage-how-to-create-file-share.md)。 
    為了獲得最佳效能，Microsoft 建議您的檔案共用與計畫存取共用的地方位於相同區域。

5.  **藉由使用儲存體帳戶金鑰裝載 Azure 檔案共用，驗證 Azure 檔案連線。**

    若要驗證是否已正確設定虛擬機器和檔案共用，請嘗試使用儲存體帳戶金鑰裝載檔案共用。 如需詳細資料，請參閱[裝載 Azure 檔案共用並在 Windows 中存取共用](storage-how-to-use-files-windows.md)。

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>啟用 Azure AD DS 驗證您的帳戶

若要啟用 SMB 上的 Azure AD DS 驗證，Azure 檔案，您可以設定屬性在 2018 年 9 月 24 日之後建立的儲存體帳戶上使用 Azure 入口網站、 Azure PowerShell 或 Azure CLI。 設定此屬性會將儲存體帳戶註冊到關聯的 Azure AD Domain Services 部署。 然後透過 SMB 的 azure AD DS 驗證已啟用儲存體帳戶中的所有新的和現有的檔案共用。 

請記住，您可以啟用 Azure AD DS 驗證透過 SMB 在您已成功部署至 Azure AD 租用戶的 Azure AD Domain Services 之後，才。 如需詳細資訊，請參閱[必要條件](#prerequisites)。

### <a name="azure-portal"></a>Azure 入口網站

若要啟用 Azure AD DS 驗證優於 SMB [Azure 入口網站](https://portal.azure.com)，請遵循下列步驟：

1. 在 Azure 入口網站中，瀏覽至您現有的儲存體帳戶，或[建立儲存體帳戶](../common/storage-quickstart-create-account.md)。
2. 在 [設定]  區段中，選取 [組態]  。
3. 啟用 [適用於 Azure 檔案服務的 Azure Active Directory 驗證 (預覽)]  。

下圖顯示如何針對您的儲存體帳戶啟用透過 SMB 進行 Azure AD 驗證。

![在 Azure 入口網站中啟用透過 SMB 進行 Azure AD 驗證](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

若要透過 SMB 使用 Azure PowerShell 啟用 Azure AD DS 驗證：首先，安裝最新的 Az 模組 （2.4 或更新版本） 或 Az.Storage 模組 （1.5 或更新版本）。如需有關如何安裝 PowerShell 的詳細資訊，請參閱 <<c0> [ 使用 PowerShellGet 的 Windows 上安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

接下來，建立新的儲存體帳戶，然後呼叫[組 AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount)並設定**EnableAzureActiveDirectoryDomainServicesForFile**參數**true**。 在以下範例中，請記得以您自己的值取代預留位置值。 (如果您使用先前的 Preview 模組，功能啟用的參數是**EnableAzureFilesAadIntegrationForSMB**。)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
若要啟用這項功能，在現有的儲存體帳戶上，使用下列命令：

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

若要從 Azure CLI 2.0 啟用透過 SMB 進行 Azure AD 驗證，請先安裝 `storage-preview` 擴充功能：

```cli-interactive
az extension add --name storage-preview
```
  
接下來，建立新的儲存體帳戶，然後呼叫 [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update)，並將 `--file-aad` 屬性設定為 **true**。 在以下範例中，請記得以您自己的值取代預留位置值。

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>將存取權限指派給身分識別 

若要使用 Azure AD 認證存取 Azure 檔案資源，身分識別 (使用者、群組或服務主體) 必須具有共用層級的必要權限。 此程序類似於指定 Windows 共用權限，您可以從中指定給定之使用者對檔案共用的存取類型。 本節中的指引示範如何將檔案共用的讀取、寫入或刪除權限指派給身分識別。

我們引進了兩個共用層級權限授與使用者的 Azure 內建角色：儲存檔案資料 SMB 共用讀取器和儲存體檔案資料的 SMB 共用參與者。 

- **儲存體檔案資料的 SMB 共用讀者**允許讀取存取 Azure 儲存體檔案共用中的透過 SMB
- **儲存體檔案資料的 SMB 共用參與者**允許讀取、 寫入和刪除存取 Azure 儲存體檔案共用中透過 SMB

> [!IMPORTANT]
> 完整管理控制檔案共用 (包括將角色指派給身分識別的能力) 需要使用儲存體帳戶金鑰。 Azure AD 認證不支援管理控制。 

您可以使用 Azure 入口網站、 PowerShell 或 Azure CLI，將指派給使用者的 Azure AD 身分識別的內建的角色，授與共用層級權限。 

#### <a name="azure-portal"></a>Azure 入口網站
將 RBAC 角色指派給 Azure AD 身分識別，使用[Azure 入口網站](https://portal.azure.com)，請遵循下列步驟：

1. 在 Azure 入口網站中，瀏覽至您的檔案共用，或[Azure 檔案服務中建立檔案共用](storage-how-to-create-file-share.md)。
2. 選取 [存取控制 (IAM)]  。
3. 選取**新增角色指派**
4. 在 **新增的角色指派**刀鋒視窗中，選取適當的內建角色 （儲存體檔案資料的 SMB 共用參與者中的 儲存體檔案資料 SMB 共用讀取器），從**角色**下拉式清單中。 保持**存取權指派給**為預設值：「 Azure AD 使用者、 群組或服務主體 」，並選取 依姓名或電子郵件地址 Azure AD 身分識別的目標。 
5. 最後，選取**儲存**來完成角色指派作業。

#### <a name="powershell"></a>PowerShell

下列 PowerShell 命令示範如何將 RBAC 角色指派給 Azure AD 身分識別，以登入名稱為基礎。 如需使用 PowerShell 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure PowerShell 管理存取](../../role-based-access-control/role-assignments-powershell.md)。

執行下列範例指令碼時，請記得使用您自己的值來取代預留位置值 (包含括弧)。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
下列的 CLI 2.0 命令會顯示如何將 RBAC 角色指派給 Azure AD 身分識別，以登入名稱為基礎。 如需使用 Azure CLI 指派 RBAC 角色的詳細資訊，請參閱[使用 RBAC 和 Azure CLI 管理存取](../../role-based-access-control/role-assignments-cli.md)。 

執行下列範例指令碼時，請記得使用您自己的值來取代預留位置值 (包含括弧)。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>透過 SMB 設定 NTFS 權限 
使用 RBAC 指派共用層級權限之後，必須在根、目錄或檔案層級指派適當的 NTFS 權限。 將共用層級權限視為高階閘道管理員，可決定使用者是否可存取共用，而 NTFS 權限可進行更精細的處理，以決定使用者可以在目錄或檔案層級執行哪些作業。 

Azure 檔案支援全套 NTFS 基本和進階權限。 您可以藉由裝載共用，然後執行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 或 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 命令，以檢視及設定 Azure 檔案共用中目錄與檔案的 NTFS 權限。 

> [!NOTE]
> 預覽版本僅支援使用 Windows 檔案總管檢視權限。 尚未支援編輯權限。

若要使用超級使用者權限設定 NTFS 權限，必須使用儲存體帳戶金鑰，從已加入網域的 VM 裝載共用。 依照下一節中的說明，從命令提示字元裝載 Azure 檔案共用，並視情況設定 NTFS 權限。

檔案共用的根目錄支援下列權限集合：

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>從命令提示字元裝載檔案共用

使用 Windows **net use** 命令以裝載 Azure 檔案共用。 請記得使用您自己的值取代預留位置值。 如需裝載檔案共用的詳細資訊，請參閱[裝載 Azure 檔案共用並存取 Windows 中的共用](storage-how-to-use-files-windows.md)。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>使用 icacls 設定 NTFS 權限
使用以下 Windows 命令，授與完整權限至檔案共用下的所有目錄和檔案，包括根目錄。 請記得使用您自己的值取代範例中括號內的預留位置值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

如需如何使用 icacls 設定 NTFS 權限，以及支援之不同類型權限的詳細資訊，請參閱 [icacls 的命令列參考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>從已加入網域的 VM 中裝載檔案共用 

現在，您已準備好使用 Azure AD 認證，從加入網域的虛擬機器存取 Azure 檔案共用，以驗證您是否成功完成上述步驟。 首先，使用已授與權限的 Azure AD 身分識別登入虛擬機器，如下圖所示。

![螢幕擷取畫面顯示使用者驗證的 Azure AD 登入畫面](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

接著，使用下列命令，裝載 Azure 檔案共用。 請記得以您自己的值取代預留位置值。 由於您已經過驗證，因此不需要提供儲存體帳戶金鑰或 Azure AD 使用者名稱和密碼。 「透過 SMB 進行 Azure AD 驗證」支援使用 Azure AD 認證的單一登入體驗。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

您現在已成功啟用「透過 SMB 進行 Azure AD 驗證」，並指派一個自訂角色，該角色提供對 Azure AD 身分識別的檔案共用存取權。 若要向其他使用者授與對檔案共用的存取權，請遵循步驟 2 和 3 中提供的指示進行操作。

## <a name="next-steps"></a>後續步驟

如需 Azure 檔案以及使用「透過 SMB 進行 Azure AD 驗證」的詳細資訊，請參閱下列資源：

- [Azure 檔案服務簡介](storage-files-introduction.md)
- [適用於 Azure 檔案之「透過 SMB 進行 Azure AD 驗證」的概觀 (預覽)](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
