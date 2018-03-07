---
title: "在 Azure Site Recovery 中使用雲端解決方案提供者 (CSP) 計畫管理多租用戶 | Microsoft Docs"
description: "說明如何透過 CSP 建立和管理租用戶訂用帳戶，並在多租用戶設定中部署 Azure Site Recovery"
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
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>使用雲端解決方案提供者 (CSP) 計畫管理多租用戶

[CSP 計畫](https://partner.microsoft.com/en-US/cloud-solution-provider)會針對 Microsoft 雲端服務 (包括 Office 365、Enterprise Mobility Suite 和 Microsoft Azure) 樹立雙贏的案例。 藉由 CSP，合作夥伴全面掌握與客戶的端對端關係，並且成為主要的關係連絡窗口。 合作夥伴可以為客戶部署 Azure 訂用帳戶，並將這些訂用帳戶與他們自己的加值自訂優惠結合。

藉由 Azure Site Recovery，合作夥伴可以直接透過 CSP 管理客戶的完整災害復原解決方案。 或者，也可以使用 CSP 設定 Site Recovery 環境，並讓客戶藉由自助服務的方式管理自己的災害復原需求。 在這兩種情況下，合作夥伴是 Site Recovery 與客戶之間的連絡窗口。 合作夥伴負責維護客戶關係，以及向客戶收取 Site Recovery 使用量的費用。

本文說明合作夥伴如何針對租用戶 VMware 設定，透過 CSP 建立和管理租用戶訂用帳戶。

## <a name="prerequisites"></a>先決條件

- [準備](tutorial-prepare-azure.md) Azure 資源，包括 Azure 訂用帳戶、Azure 虛擬網路和儲存體帳戶。
- [準備](tutorial-prepare-on-premises-vmware.md) VMware 內部部署 VMware 伺服器和 VM。
- 針對每個租用戶建立可以和租用戶虛擬機器及合作夥伴的 vCenter 通訊的個別「管理伺服器」。 只有合作夥伴有此伺服器的存取權限。 如需不同多租用戶環境的詳細資訊，請參閱[多租用戶 VMware](site-recovery-multi-tenant-support-vmware-using-csp.md) 指引。

## <a name="create-a-tenant-account"></a>建立租用戶帳戶

1. 透過 [Microsoft 合作夥伴中心](https://partnercenter.microsoft.com/) 登入 CSP 帳戶。

2. 在 [儀表板] 功能表上，選取 [客戶]。

    ![Microsoft 夥伴中心客戶連結](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. 在開啟的頁面上，按一下 [新增客戶] 按鈕。

    ![[新增客戶] 按鈕](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. 在 [新客戶] 頁面上，填入租用戶的所有帳戶資訊詳細資料，然後按一下 [下一步: 訂閱]。

    ![[帳戶資訊] 頁面](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. 在訂閱選取頁面上，勾選 [Microsoft Azure] 核取方塊。 您可以立即或在其他任何時候新增其他訂用帳戶。

    ![[Microsoft Azure 訂用帳戶] 核取方塊](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. 在 [檢閱] 頁面上，確認租用戶詳細資料，然後按一下 [提交]。

    ![[檢閱] 頁面](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    您建立租用戶帳戶後，確認網頁隨即出現，顯示預設帳戶的詳細資料和該訂用帳戶的密碼。

7. 儲存資訊並在稍後視需要透過 Azure 入口網站登入網頁來變更密碼。  

    您可以與租用戶共用這些既有的資訊，也可以視需要建立和共用不同的帳戶。

## <a name="access-the-tenant-account"></a>存取租用戶帳戶

如「步驟 1：建立租用戶帳戶」所述，您可以透過 Microsoft 夥伴中心儀表板存取租用戶的訂用帳戶。

1. 移至 [客戶] 頁面，然後按一下租用戶帳戶的名稱。

2. 在租用戶帳戶的 [訂用帳戶] 頁面上，您可以監視現有帳戶的訂用帳戶，並且視需要新增更多訂用帳戶。 若要管理租用戶的災復原作業，請選取 [所有資源 (Azure 入口網站)]。

    ![[所有資源] 連結](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    按一下 [所有資源] 將授與租用戶的 Azure 訂用帳戶存取權限。 您可以按一下 Azure 入口網站右上方的 [Azure Active Directory] 連結，以確認存取權限。

    ![[Azure Active Directory] 連結](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

您現在可以透過 Azure 入口網站為租用戶執行所有 Site Recovery 作業，以及管理災害復原作業。 若要透過 CSP 存取租用戶訂用帳戶來進行受控災害復原，請遵循先前所述的程序。

## <a name="deploy-resources-to-the-tenant-subscription"></a>將資源部署到租用戶訂用帳戶
1. 在 Azure 入口網站上，建立「資源群組」，然後對於每個一般程序部署「復原服務」保存庫。

2. 下載保存庫註冊金鑰。

3. 使用保存庫註冊金鑰為租用戶註冊 CS。

4. 輸入兩個存取帳戶 (vCenter 存取帳戶和虛擬機器存取帳戶) 的認證。

    ![管理員設定伺服器帳戶](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>向復原服務保存庫註冊 Site Recovery 基礎結構
1. 在 Azure 入口網站中，對於稍早建立的保存庫，將 vCenter 伺服器註冊到您在「步驟 3：將資源部署到租用戶訂用帳戶」中註冊的 CS。 將 vCenter 存取帳戶用於此目的。
2. 針對每個一般程序的 Site Recovery 完成「準備基礎結構」程序。
3. 現在可以開始複寫 VM。 確認 [複寫] 選項下的 [選取虛擬機器] 刀鋒視窗僅顯示租用戶的虛擬機器。

    ![[選取虛擬機器] 刀鋒視窗上的租用戶虛擬機器清單](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>為租用戶指派訂用帳戶的存取權

對於自助災害復原，向租用戶提供帳戶詳細資料，如「步驟 1：建立租用戶帳戶」一節的步驟 6 所述。 合作夥伴設定災害復原基礎結構之後，請執行此動作。 無論災害復原類型是否為受控或自助，合作夥伴都必須透過 CSP 入口網站存取租用戶訂用帳戶。 合作夥伴可設定合作夥伴擁有的保存庫，並將基礎結構註冊到租用戶訂用帳戶。

合作夥伴也可以透過 CSP 入口網站將新的使用者加入租用戶訂用帳戶，方法如下：

1. 移至租用戶的 CSP 訂用帳戶頁面，然後選取 [使用者與授權] 選項。

    ![租用戶的 CSP 訂用帳戶頁面](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    您現在可以藉由輸入相關詳細資料並選取權限，或是使用 CSV 檔案上傳使用者清單，來建立新的使用者。

2. 建立新的使用者之後，返回 Azure 入口網站，並在 [訂用帳戶] 刀鋒視窗選取相關訂用帳戶。

3. 在開啟的刀鋒視窗上選取 [存取控制 (IAM)]，然後按一下 [新增] 以使用相關的存取層級新增使用者。      
    在按一下存取層級之後開啟的刀鋒視窗上，會自動顯示透過 CSP 入口網站建立的使用者。

    ![新增使用者](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    *參與者*角色就足以執行大多數的管理作業。 除了變更存取層級 (必須具有「擁有者」存取層級) 之外，具有此存取層級的使用者可以在訂用帳戶上執行任何作業。

  Azure Site Recovery 也有三個[預先定義的使用者角色](site-recovery-role-based-linked-access-control.md)，可視需要用來進一步限制存取層級。

## <a name="next-steps"></a>後續步驟
  [深入了解](site-recovery-role-based-linked-access-control.md)如何使用角色型存取控制來管理 Azure Site Recovery 部署。

  [管理多租用戶 VMware 環境](site-recovery-multi-tenant-support-vmware-using-csp.md)
