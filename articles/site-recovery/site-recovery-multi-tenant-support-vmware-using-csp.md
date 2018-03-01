---
title: "VMware VM 複寫至 Azure (CSP 計劃) 的多租用戶支援 | Microsoft Docs"
description: "描述如何使用 Azure 入口網站在多租用戶環境中部署 Azure Site Recovery，以透過 CSP 計畫協調內部部署 VMware 虛擬機器 (VM) 至 Azure 的複寫、容錯移轉和復原"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 532dd399d2d5fcbab616744dd02f4a95078cbb1b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Azure Site Recovery 中的多租用戶支援，適用於透過 CSP 將 VMware 虛擬機器複寫到 Azure

Azure Site Recovery 支援租用戶訂用帳戶的多租用戶環境。 對於透過 Microsoft Cloud 解決方案提供者 (CSP) 方案建立及管理的租用戶訂用帳戶，它也支援多租用戶。 本文中詳述實作及管理多租用戶 VMware 到 Azure 案例的指南。 如需建立及管理租用戶訂用帳戶的詳細資訊，請參閱[使用 CSP 管理多租用戶](site-recovery-manage-multi-tenancy-with-csp.md)。

本指南與複寫 VMware 虛擬機器到 Azure 的現有文件密切相關。 如需詳細資訊，請參閱[使用 Site Recovery 將 VMware 虛擬機器複寫至 Azure](site-recovery-vmware-to-azure.md)。

## <a name="multi-tenant-environments"></a>多租用戶環境
多租用戶模型主要有三種：

* **共用主機服務提供者 (HSP)**：合作夥伴擁有實體基礎結構，而使用者則共用資源 (如 vCenter、資料中心、實體儲存體等) 以將多租用戶的虛擬機器裝載在同一個基礎結構。 合作夥伴可提供災害復原管理做為受控服務，租用戶也可以擁有災害復原做為自助服務方案。

* **專用主機服務提供者**：合作夥伴擁有實體基礎結構，但使用專用資源 (如多個 vCenters、實體資料存放區等) 在個別的基礎結構上裝載每個租用戶的虛擬機器。 合作夥伴可提供災害復原管理做為受控服務，租用戶也可以擁有災害復原做為自助服務方案。

* 
            **受控服務提供者 (MSP)** – 客戶擁有裝載虛擬機器的實體基礎結構，而合作夥伴則提供災害復原支援及管理。

## <a name="shared-hosting-multi-tenant-guidance"></a>共用主機多租用戶指南
本節詳述共用主機案例。 另外兩個案例是共用主機案例的子集，且使用相同的原則。 共用主機指南最後面會詳述其中的差異。

多租用戶案例的基本需求是隔離不同的租用戶。 一個租用戶無法觀察另一個租用戶裝載的內容。 這個需求在自助管理環境中，比在合作夥伴管理的環境中來得重要，甚至有重大影響。 本指南假設租用戶隔離是必要條件。

下圖說明這個架構：

![含一個 vCenter 的共用 HSP](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**含一個 vCenter 的共用主機案例**

如上圖所示，每個客戶都有個別的管理伺服器。 這個設定可以將租用戶限制於只能存取租用戶特定虛擬機器，以達到租用戶隔離的目的。 VMware 虛擬機器複寫案例會使用組態伺服器來管理帳戶以探索虛擬機器及安裝代理程式。 相同的原則適用於多租用戶環境，但是另外透過 vCenter 存取控制來限制 VM 探索。

資料隔離的需求會使得基礎結構機密資訊 (如存取認證) 對租用戶保持公開。 基於這個理由，建議所有管理伺服器的元件都維持在合作夥伴的獨佔控制之下。 管理伺服器元件包括：
* 組態伺服器 (CS)
* 處理序伺服器 (PS)
* 主要目標伺服器 (MT)

向外延展 PS 也是在合作夥伴的控制之下。

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>多租用戶案例中的每個 CS 都使用兩個帳戶

- **vCenter 存取帳戶**：此帳戶可用來探索租用戶虛擬機器。 帳戶有獲得指派的 vCenter 存取權限 (如下一節所述)。 為了避免意外的存取權限洩漏，建議夥伴自行在設定工具中輸入這些認證。

- **虛擬機器存取帳戶**：此帳戶是用來在租用戶上，透過自動推送安裝行動代理程式。 這通常是租用戶可能會提供給合作夥伴的網域帳戶，或可能由合作夥伴直接管理的網域帳戶。 如果租用戶不想直接與合作夥伴分享詳細資料，他們可以透過限時的 CS 存取權來輸入認證，也可以在合作夥伴的協助下手動安裝行動代理程式。

### <a name="requirements-for-a-vcenter-access-account"></a>vCenter 存取帳戶的需求

如前一節所述，您必須使用獲指派特殊角色的帳戶設定 CS。 針對每個 vCenter 物件，必須將角色指派套用於 vCenter 存取帳戶，而且不要傳播到子物件。 此設定可以確保租用戶隔離，因為存取傳播可能會導致意外存取其他物件。

![傳播到子物件選項](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

替代方案是在資料中心物件指派使用者帳戶和角色，並將這些傳播到子物件。 然後將每個物件 (例如其他租用戶的虛擬機器) 的*無存取*角色授與應該無法存取特定租用戶的帳戶。 這個設定相當繁瑣，而且會意外公開存取控制，因為會對每個新的子物件自動授與從父物件繼承的存取權。 因此建議採用第一種方法。

vCenter 帳戶存取程序如下：

1. 藉由複製預先定義的「唯讀」角色來建立新的角色，並以易記名稱 (例如此範例中顯示的 Azure_Site_Recovery) 命名。

2. 將下列權限指派給這個角色：

    * **資料存放區**：配置空間、瀏覽資料存放區、底層檔案作業、移除檔案、更新虛擬機器檔案

    * **網路**：網路指派

    * **資源**：指派虛擬機器至資源集區、移轉已關閉電源的虛擬機器、移轉已開啟電源的虛擬機器

    * **工作**：建立工作、更新工作

    * **虛擬機器**：
        * 組態 > 全部
        * 互動 > 回答問題、裝置連線、設定 CD 媒體、設定磁碟片媒體、電源關閉、電源開啟、VMware 工具安裝
        * 清查 > 從現有建立、建立新的、註冊、取消註冊
        * 佈建 > 允許虛擬機器下載、允許虛擬機器檔案上傳
        * 快照集管理 > 移除快照集

    ![[編輯角色] 對話方塊](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. 針對不同物件，指派存取層級給 vCenter 帳戶 (在租用戶 CS 中使用)：

>| Object | 角色 | 備註 |
>| --- | --- | --- |
>| vCenter | 唯讀 | 只用以允許 vCenter 存取以管理其他物件。 如果帳戶不會提供給租用戶，或用於任何 vCenter 上的管理作業，則可以移除此權限。 |
>| 資料中心 | Azure_Site_Recovery |  |
>| 主機和主機叢集 | Azure_Site_Recovery | 再次確認存取權在物件層級，以限制只有可存取的主機在容錯移轉前和容錯回復後有租用戶虛擬機器。 |
>| 資料存放區和資料存放區叢集 | Azure_Site_Recovery | 同上。 |
>| 網路 | Azure_Site_Recovery |  |
>| 管理伺服器 | Azure_Site_Recovery | 包含 CS 以外所有元件 (CS、PS 及 MT) 的存取權。 |
>| 租用戶 VM | Azure_Site_Recovery | 確保特定租用戶的任何新租用戶虛擬機器也會有此存取權，否則將無法透過 Azure 入口網站探索它們。 |

現在已經完成 vCenter 帳戶存取權。 這個步驟可以滿足完成容錯回復作業的最小權限需求。 也可以搭配現有的原則使用這些存取權限。 只要將現有的權限集修改成包含上面步驟 2 詳述的角色權限即可。

若要限制災害復原作業僅進行到容錯移轉狀態，而沒有容錯回復的功能，請遵循上述程序，但是不要指派 *Azure_Site_Recovery*給 vCenter 存取帳戶，而是改為只指派*唯讀*角色。 這個權限集合會允許虛擬機器複寫和容錯移轉，而不允許容錯回復。 以上程序的其餘部分都保持原狀。 為了確保租用戶隔離及限制虛擬機器探索，所有權限仍僅指派到物件層級而未傳播到子物件。

## <a name="other-multi-tenant-environments"></a>其他多租用戶環境

上節描述如何針對共用主機解決方案設定多租用戶環境。 另外兩個主要解決方案是專用主機和受控服務。 下列各節將說明這些解決方案的架構。

### <a name="dedicated-hosting-solution"></a>專用主機解決方案

如下圖所示，專用主機方案的架構差異是每個租用戶的基礎結構完全針對該租用戶設定。 因為租用戶是透過個別的 vCenter 隔離，所以主機提供者仍必須按照針對共用主機提供的 CSP 步驟來執行，但不需要擔心租用戶隔離。 CSP 安裝程式會保持不變。

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**含多個 vCenters 的專用主機案例**

### <a name="managed-service-solution"></a>受控服務解決方案

如下圖所示，受控服務方案本身的架構差異是每個租用戶的基礎結構在實體上與其他租用戶的基礎結構分開。 此案例通常是在租用戶擁有基礎結構時存在，而且需要解決方案提供者來管理災害復原。 同樣地，因為租用戶在實體上透過不同的基礎結構來隔離，所以合作夥伴仍需要按照針對共用主機提供的 CSP 步驟來執行，但不需要擔心租用戶隔離。 CSP 佈建保持不變。

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  

            **含多個 vCenters 的受控服務案例**

## <a name="next-steps"></a>後續步驟
[深入了解](site-recovery-role-based-linked-access-control.md)如何使用角色型存取控制來管理 Azure Site Recovery 部署。

[透過 CSP 管理多租用戶](site-recovery-manage-multi-tenancy-with-csp.md)
