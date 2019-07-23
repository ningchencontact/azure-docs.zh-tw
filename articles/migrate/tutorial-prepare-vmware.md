---
title: 使用 Azure Migrate 準備 VMware VM 以進行評量並移轉至 Azure | Microsoft Docs
description: 說明如何使用 Azure Migrate 來準備內部部署 VMware VM 的評量並將其移轉至 Azure。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 10f559295ff0598dea26fb30b089f020e2985889
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840333"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>準備 VMware VM 以進行評量並移轉至 Azure

本文將說明如何使用 [Azure Migrate](migrate-services-overview.md) 來準備內部部署 VMware VM 的評量並將其移轉至 Azure。

[Azure Migrate](migrate-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。 


本教學課程是一個系列中的第一篇，說明如何評估及遷移 VMware VM。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 準備 Azure。 設定 Azure 帳戶和資源的權限，以搭配使用 Azure Migrate。
> * 準備內部部署 VMware 伺服器和 VM 來評估 VM。
> * 準備內部部署 VMware 伺服器和 VM 來遷移 VM。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程會在情況允許時都使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請參閱 VMware 評量和移轉的操作說明。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/pricing/free-trial/) 。


## <a name="prepare-azure"></a>準備 Azure

您需要這些 Azure 權限：

- 您的 Azure 帳戶需要可建立 Azure Migrate 專案的權限，才能進行評估和遷移。 
- 進行 VMware VM 的評量和無代理程式移轉時，Azure Migrate 會執行用於探索 VM 的輕量設備，並將 VM 中繼資料和效能資料傳送給 Azure Migrate。 在 Azure 中，您需要可註冊 Azure Migrate 設備的權限。
- 若要使用 Azure Migrate 伺服器移轉來遷移 VMware VM，Azure Migrate 會在資源群組中建立 Key Vault，以管理訂用帳戶中複寫儲存體帳戶的存取金鑰。 若要建立保存庫，您在 Azure Migrate 專案所在的資源群組上必須有角色指派權限。 


### <a name="assign-permissions-to-create-project"></a>指派建立專案的權限

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]  。
2. 在 [檢查存取權]  中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」  或「擁有者」  權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作來指派角色。

### <a name="assign-permissions-to-register-the-appliance"></a>指派權限以註冊設備

如果您要部署 Azure Migrate 設備來對 VM 進行評量或無代理程式移轉，您就需要註冊設備。

- 在註冊設備期間，Azure Migrate 會建立兩個可唯一識別設備的 Azure Active Directory (Azure AD) 應用程式
    - 第一個應用程式會與 Azure Migrate 服務端點進行通訊。
    - 第二個應用程式會存取在註冊期間建立的 Azure Key Vault，以儲存 Azure AD 的應用程式資訊和設備組態設定。
- 您可以使用下列其中一種方法來指派 Azure Migrate 的權限，以建立這些 Azure AD 應用程式：
    - 租用戶/全域管理員可以授與權限給租用戶中的使用者，以建立及註冊 Azure AD 應用程式。
    - 租用戶/全域管理員可以為帳戶指派應用程式開發人員角色 (具有權限)。

值得注意的是：

- 除了所述的訂用帳戶外，應用程式沒有任何其他訂用帳戶的存取權限。
- 只有當您註冊新的設備時，才需要這些權限。 完成設備的設定後，您可以移除權限。 


#### <a name="grant-account-permissions"></a>授與帳戶權限

租用戶/全域管理員可以授與權限，如下所示

1. 在 Azure AD 中，租用戶/全域管理員應該瀏覽至 [Azure Active Directory]   > [使用者]   > [使用者設定]  。
2. 管理員應將 [應用程式註冊]  設定為 [是]  。

    ![Azure AD 權限](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> 這是一個不敏感的預設設定。 [深入了解](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>指派應用程式開發人員角色 

租用戶/全域管理員可以為帳戶指派應用程式開發人員角色。 [深入了解](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

## <a name="assign-role-assignment-permissions"></a>指派角色指派權限

在 Azure Migrate 專案所在的資源群組上指派角色指派權限，如下所示：

1. 在 Azure 入口網站中的資源群組中，選取 [存取控制 (IAM)]  。
2. 在 [檢查存取權]  中，尋找相關的帳戶，然後按一下以查看權限。

    - 若要執行伺服器評量，「參與者」  權限就已足夠。
    - 若要執行無代理程式的伺服器移轉，您應具有「擁有者」  (或「參與者」  及「使用者存取系統管理員」  ) 權限。

3. 如果您沒有必要權限，請向資源群組擁有者提出要求。 



## <a name="prepare-for-vmware-vm-assessment"></a>準備 VMware VM 評量

若要準備 VMware VM 評量，您必須確認 Hyper-V 主機和 VM 設定，以及確認設備部署的設定。

### <a name="verify-vmware-settings"></a>確認 VMware 設定

1. [確認](migrate-support-matrix-vmware.md#assessment-vmware-server-requirements)用於評估 VM 的 VMware 伺服器需求。
2. [確認](migrate-support-matrix-vmware.md#assessment-port-requirements) vCenter 伺服器上已開啟必要的連接埠。


### <a name="set-up-an-account-for-assessment"></a>設定用於評量的帳戶

Azure Migrate 需要存取 vCenter Server，才能探索用於評估和無代理程式移轉的 VM。 若只是要進行評估，您需要一個可使用 vCenter Server 的唯讀帳戶。

如果您使用以 URL 為基礎的 firewall.proxy，請允許必要 [Azure URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements) 的存取權。

請確定 Proxy 會解析在查詢 URL 時所收到的任何 CNAME 記錄。


### <a name="verify-appliance-settings-for-assessment"></a>確認用於評量的設備設定

在下一個教學課程中設定 Azure Migrate 設備並開始進行評量之前，請先準備設備部署。

1. [確認](migrate-support-matrix-vmware.md#assessment-appliance-requirements)在 VMware 中設定 Azure Migrate 設備的需求。
2. [檢閱](migrate-support-matrix-vmware.md#assessment-url-access-requirements)設備需要存取的 Azure URL。
3. 檢閱設備將在探索和評量期間收集的資料。
4. [注意](migrate-support-matrix-vmware.md#assessment-port-requirements)設備的連接埠存取需求。
5. 您可以使用 OVA 檔案，將 Azure Migrate 設備部署為 VMware VM。 在 vCenter Server 上，確定您的帳戶有權使用 OVA 檔案建立 VM。


## <a name="prepare-for-agentless-vmware-migration"></a>準備無代理程式的 VMware 移轉

請檢閱 VMware VM 無代理程式移轉的需求。

1. [檢閱](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements)無代理程式移轉的 VMware 伺服器需求。
2. 設定帳戶以透過無代理程式移轉[所需的權限](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions)來存取 vCenter Server。
3. [注意](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)透過無代理程式移轉將 VMware VM 遷移至 Azure 的需求。
4. [檢閱](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements)無代理程式移轉的設備需求。]
5. 請注意無代理程式移轉的設備 [URL 存取](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements)和[連接埠存取](migrate-support-matrix-vmware.md#agentless-migration-port-requirements)需求。


## <a name="prepare-for-agent-based-vmware-migration"></a>準備代理程式型 VMware 移轉

請檢閱代理程式型 VMware VM 移轉的需求。

1. [檢閱](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements)無代理程式移轉的 VMware 伺服器需求。 
2. 設定帳戶以透過無代理程式移轉[所需的權限](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions)來存取 vCenter Server。
3. [注意](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements)使用代理程式型移轉將 VMware VM 遷移至 Azure 時的需求，包括在每個要遷移的 VM 上安裝行動服務。
4. 注意 [URL 存取](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements)。
5. 針對每部 VM 上執行的行動服務，以及 Azure Migrate 設定伺服器，檢閱[連接埠存取](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements)需求。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：
 
> [!div class="checklist"] 
> * 設定 Azure 權限。
> * 準備VMware 以進行評估及移轉。


請繼續進行第二個教學課程，以設定 Azure Migrate 專案，並評估要遷移至 Azure 的 VMware VM。

> [!div class="nextstepaction"] 
> [評估 VMware VM](./tutorial-migrate-vmware.md) 

