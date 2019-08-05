---
title: 使用 Azure Migrate 準備 Hyper-V VM 以進行評量並移轉至 Azure | Microsoft Docs
description: 說明如何使用 Azure Migrate 來準備 Hyper-V VM 的評量並將其移轉至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 514905bf2db1c0c58faa131eeb916af033b2c830
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640834"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>準備 Hyper-V VM 的評量並將其移轉至 Azure

本文將說明如何使用 [Azure Migrate](migrate-services-overview.md) 來準備內部部署的 Hyper-V VM 評量並將其移轉至 Azure。

[Azure Migrate](migrate-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 

本教學課程是一個系列中的第一篇，說明如何評估 Hyper-V VM 並將其遷移至 Azure。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 準備 Azure。 設定 Azure 帳戶和資源的權限，以搭配使用 Azure Migrate。
> * 準備內部部署 Hyper-V 主機和 VM 以進行伺服器評量。
> * 準備內部部署 Hyper-V 主機和 VM 以進行伺服器移轉。


> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請參閱 Hyper-V 評量和移轉的操作說明。


如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。


## <a name="prepare-azure"></a>準備 Azure

### <a name="azure-permissions"></a>Azure 權限

您需要 Azure Migrate 部署的設定權限。

- 讓 Azure 帳戶可建立 Azure Migrate 專案的權限。 
- 讓帳戶可註冊 Azure Migrate 設備的權限。 用於 Hyper-V 探索和移轉的設備。 在註冊設備期間，Azure Migrate 會建立兩個可唯一識別設備的 Azure Active Directory (Azure AD) 應用程式：
    - 第一個應用程式會與 Azure Migrate 服務端點進行通訊。
    - 第二個應用程式會存取在註冊期間建立的 Azure Key Vault，以儲存 Azure AD 的應用程式資訊和設備組態設定。



### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限

檢查您是否有建立 Azure Migrate 專案的權限。

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]  。
2. 在 [檢查存取權]  中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」  或「擁有者」  權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作來指派角色。


### <a name="assign-permissions-to-register-the-appliance"></a>指派權限以註冊設備

您可以使用下列其中一種方法來指派 Azure Migrate 的權限，以在設備註冊期間建立 Azure AD 應用程式：

- 租用戶/全域管理員可將權限授與租用戶中的使用者，以建立及註冊 Azure AD 應用程式。
- 租用戶/全域管理員可為帳戶指派應用程式開發人員角色 (具有權限)。

值得注意的是：

- 除了所述的訂用帳戶外，應用程式沒有任何其他訂用帳戶的存取權限。
- 只有當您註冊新的設備時，才需要這些權限。 完成設備的設定後，您可以移除權限。 


#### <a name="grant-account-permissions"></a>授與帳戶權限

租用戶/全域管理員可以授與權限，如下所示：

1. 在 Azure AD 中，租用戶/全域管理員應該瀏覽至 [Azure Active Directory]   > [使用者]   > [使用者設定]  。
2. 管理員應將 [應用程式註冊]  設定為 [是]  。

    ![Azure AD 權限](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 這是一個不敏感的預設設定。 [深入了解](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>指派應用程式開發人員角色 

租用戶/全域管理員可為帳戶指派應用程式開發人員角色。 [深入了解](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。


## <a name="prepare-for-hyper-v-assessment"></a>準備 Hyper-V 評量

若要準備 Hyper-V 評量，請執行下列動作：

1. 確認 Hyper-V 主機設定。
2. 在每部主機上設定 PowerShell 遠端功能，讓 Azure Migrate 設備可以透過 WinRM 連線在主機上執行 PowerShell 命令。
3. 如果 VM 磁碟位於遠端 SMB 存放裝置，則需要委派認證。 
    - 啟用 CredSSP 委派，讓 Azure Migrate 設備可作為用戶端，並將認證委派給主機。 T
    - 您可以讓每部主機成為設備的委派，如下所述。
    - 之後，當您設定設備時，您就可以在設備上啟用委派。
4. 檢閱設備需求及設備所需的 URL/連接埠存取權。
5. 設定設備將用來探索 VM 的帳戶。
6. 在您想要探索和評估的每部 VM 上設定 Hyper-V Integration Services。


您可以使用下列程序，手動進行這些設定。 或是，您可以執行 Hyper-V 必要條件組態指令碼。

### <a name="hyper-v-prerequisites-configuration-script"></a>Hyper-V 必要條件組態指令碼

此指令碼會驗證 Hyper-V 主機，並完成探索及評估 Hyper-V VM 所需的設定。 以下是其作用：

- 檢查您是否在支援的 PowerShell 版本上執行指令碼。
- 驗證您 (執行指令碼的使用者) 是否有 Hyper-V 主機上的系統管理權限。
- 可讓您建立本機使用者帳戶 (非系統管理員)，以便 Azure Migrate 服務與 Hyper-V 主機通訊。 此使用者帳戶會新增至主機上的這些群組：
    - 遠端管理使用者
    - Hyper-V 系統管理員
    - 效能監視器使用者
- 檢查主機是否正在執行支援的 Hyper-V 版本和 Hyper-V 角色。
- 啟用 WinRM 服務，並在主機上開啟埠 5985 (HTTP) 和 5986 (HTTPS) (用於收集中繼資料)。
- 在主機上啟用 PowerShell 遠端功能。
- 檢查由主機管理的所有 VM 是否都已啟用 Hyper-V 整合服務。 
- 視需要在主機上啟用 CredSSP。

執行指令碼，如下所示：

1. 請確定您已在 Hyper-V 主機上安裝 PowerShell 4.0 版或更新版本。
2. 從 [Microsoft 下載中心](https://aka.ms/migrate/script/hyperv)下載指令碼。 此指令碼會由 Microsoft 以密碼編譯方式進行簽署。
3. 使用 MD5 或 SHA256 雜湊檔案來驗證指令碼完整性。 執行此命令以產生指令碼的雜湊：
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    使用方式範例： 
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```
    
    雜湊值為：
    雜湊 | 值
    --- | ---
    **MD5 雜湊** | 0ef418f31915d01f896ac42a80dc414e
    **SHA256 雜湊** | 0ef418f31915d01f896ac42a80dc414e0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2


4.  驗證指令碼完整性之後，請使用此 PowerShell 命令在每部 Hyper-V 主機上執行指令碼：
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```


### <a name="verify-hyper-v-host-settings"></a>確認 Hyper-V 主機設定

1. 確認伺服器評量的 [Hyper-V 主機需求](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements)。
2. 確認 Hyper-V 主機上已開啟[必要的連接埠](migrate-support-matrix-hyper-v.md#assessment-port-requirements)。

### <a name="enable-powershell-remoting-on-hosts"></a>在主機上啟用 PowerShell 遠端功能

在每部主機上設定 PowerShell 遠端功能，如下所示：

1. 以系統管理員身分在每個主機上開啟 PowerShell 主控台。
2. 請執行這個命令：

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>在主機上啟用 CredSSP

如果主機具有 VM，且磁碟都位於 SMB 共用上，請在主機上完成此步驟。

- 您可以從遠端對所有 Hyper-V 主機執行此命令。
- 如果您在叢集上新增主機節點，系統會自動加入這些節點以進行探索，但您需要在新節點上手動啟用 CredSSP (如有需要)。

以下列方式啟用：

1. 識別 Hyper-V 主機是否執行在 SMB 共用上有磁碟的 Hyper-V VM。
2. 在每個識別到的 Hyper-V 主機上執行下列命令：

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

當您設定設備時，您可以藉由[在設備上啟用 CredSSP](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds) 來完成 CredSSP 的設定。 這會在本系列的下一個教學課程中加以說明。


### <a name="verify-appliance-settings"></a>確認設備設定

在下一個教學課程中設定 Azure Migrate 設備並開始進行評量之前，請先準備設備部署。

1. [確認](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)設備需求。
2. [檢閱](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)設備需要存取的 Azure URL。
3. 檢閱設備將在探索和評量期間收集的資料。
4. [注意](migrate-support-matrix-hyper-v.md#assessment-port-requirements)設備的連接埠存取需求。


### <a name="set-up-an-account-for-vm-discovery"></a>設定用於 VM 探索的帳戶

Azure Migrate 需要可探索內部部署 VM 的權限。

- 在 Hyper-V 主機/叢集上設定具有系統管理員權限的網域或本機使用者帳戶。

    - 您需要單一帳戶來使用要包含在探索中的所有主機和叢集。
    - 此帳戶可以是本機或網域帳戶。 我們建議您在 Hyper-V 主機或叢集上具有系統管理員權限。
    - 或者，如果您不想要指派系統管理員權限，則需要下列權限：
        - 遠端管理使用者
        - Hyper-V 系統管理員
        - 效能監視器使用者

### <a name="enable-integration-services-on-vms"></a>在 VM 上啟用 Integration Services

每部 VM 上都應啟用 Integration Services，才能讓 Azure Migrate 在 VM 上擷取作業系統資訊。

在您想要探索和評估的 VM 上，對每部 VM 啟用 [Hyper-V Integration Services](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。 

## <a name="prepare-for-hyper-v-migration"></a>準備 Hyper-V 移轉

1. [檢閱](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Hyper-V 主機需求以進行移轉。
2. [檢閱](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements)您要遷移至 Azure 的 Hyper-V VM 有何需求。
3. [注意](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) Hyper-V 主機和叢集進行 VM 移轉時需要存取的 Azure URL。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：
 
> [!div class="checklist"] 
> * 設定 Azure 帳戶權限。
> * 準備 Hyper-V 主機和 VM 以進行評量和移轉。

請繼續進行下一個教學課程，以建立 Azure Migrate 專案，並評估要遷移至 Azure 的 Hyper-V VM

> [!div class="nextstepaction"] 
> [評估 Hyper-V VMs](./tutorial-assess-hyper-v.md) 
