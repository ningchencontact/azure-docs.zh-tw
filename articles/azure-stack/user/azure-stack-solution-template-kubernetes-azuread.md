---
title: 使用 Azure Active Directory (Azure AD) 將 Kubernetes 部署至 Azure Stack | Microsoft Docs
description: 了解如何使用 Azure Active Directory (Azure AD) 將 Kubernetes 部署至 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: c207fb866cdd17e4db1796e415c159eb887eef08
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243550"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>使用 Azure Active Directory 將 Kubernetes 部署至 Azure Stack

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

> [!Note]  
> Azure Stack 上的 Kubernetes 處於預覽狀態。

在單一、協調的作業中使用 Azure Active Directory (Azure AD) 作為身分識別管理服務時，可以按照這篇文章中的步驟針對 Kubernetes 部署和設定資源。

## <a name="prerequisites"></a>必要條件

若要開始使用，請確定您具有適當權限，並且已備妥 Azure Stack。

1. 確認您可以在 Azure Active Directory (Azure AD) 租用戶中建立應用程式。 您必須具備這些權限才能進行 Kubernetes 部署。

    如需檢查權限的指示，請參閱[檢查 Azure Active Directory 權限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)。

1. 產生在 Azure Stack 上用來登入 Linux VM 的 SSH 公開和私密金鑰組。 您在建立叢集時將需要用到公開金鑰。

    如需產生金鑰的指示，請參閱 [SSH 金鑰產生](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)。

1. 確認您在 Azure Stack 租用戶入口網站中具有有效的訂用帳戶，且您有足夠的公用 IP 位址可用來新增應用程式。

    叢集無法部署至 Azure Stack 的**系統管理員**訂用帳戶。 您必須使用「**使用者**」訂用帳戶。 

1. 如果您的市集中沒有 Kubernetes 叢集，請連絡您的 Azure Stack 系統管理員。

## <a name="create-a-service-principal"></a>建立服務主體

在 Azure 中建立服務主體。 服務主體可讓您的應用程式存取 Azure Stack 資源。

1. 登入全域 [Azure 入口網站](http://portal.azure.com)。

1. 確認您已使用與 Azure Stack 執行個體相關聯的 Azure AD 租用戶登入。 您可以按一下 Azure 工具列中的篩選圖示切換登入。

    ![選取您的 AD 租用戶](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. 建立 Azure AD 應用程式。

    a. 選取 [Azure Active Directory] > [+ 應用程式註冊] > [新應用程式註冊]。

    b. 輸入應用程式的 [名稱]。

    c. 選取 [Web 應用程式/API]。

    d. 針對 [登入 URL] 輸入 `http://localhost`。

    c. 按一下頁面底部的 [新增] 。

1. 記下 [應用程式識別碼]。 您在建立叢集時將需要用到此識別碼。 系統會將此識別碼參考為 [服務主體用戶端識別碼]**服務主體用戶端識別碼**。

1. 選取 [設定] > [金鑰]。

    a. 輸入 [說明]。

    b. 針對 [到期日]，選取 [永久有效]。

    c. 選取 [ **儲存**]。 請記下金鑰字串。 您在建立叢集時將需要用到此金鑰字串。 系統會將此金鑰參考為 [服務主體用戶端密碼]。

## <a name="give-the-service-principal-access"></a>指定服務主體存取權

指定對訂用帳戶的服務主體存取權，讓主體能夠建立資源。

1.  登入 [Azure Stack 入口網站](https://portal.local.azurestack.external/)。

1. 選取 [所有服務] > [訂用帳戶]。

1. 選取操作員建立要使用 Kubernetes 叢集的訂用帳戶。

1. 選取 [存取控制 (IAM)] > 選取 [新增角色指派]。

1. 選取 [參與者] 角色。

1. 選取為您的服務主體建立的應用程式名稱。 您可能需要在搜尋方塊中輸入名稱。

1. 按一下 [檔案] 。

## <a name="deploy-kubernetes"></a>部署 Kubernetes

1. 開啟 [Azure Stack 入口網站](https://portal.local.azurestack.external)。

1. 選取 [+ 建立資源] > [計算] > [Kubernetes 叢集]。 按一下頁面底部的 [新增] 。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基本概念

1. 在 [建立 Kubernetes 叢集] 中選取 [基本資料]。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 選取**訂用帳戶**識別碼。

1. 輸入新資源群組的名稱，或選取現有的資源群組。 資源名稱必須是小寫的英數字元。

1. 選取資源群組的 [位置]。 這是您選擇用來安裝 Azure Stack 的區域。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes 叢集設定

1. 在 [建立 Kubernetes 叢集] 中選取 [Kubernetes 叢集設定]。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. 輸入 **Linux VM 系統管理員使用者名稱**。 這是屬於 Kubernetes 叢集和 DVM 的 Linux 虛擬機器的使用者名稱。

1. 輸入用來對建立為 Kubernetes 叢集和 DVM 一部分的所有 Linux 機器進行授權的 **SSH 公開金鑰**。

1. 輸入在區域中具有唯一性的**主要設定檔 DNS 前置詞**。 這必須是區域中唯一的名稱，例如 `k8s-12345`。 根據最佳做法，請試著選擇與資源群組名稱相同的名稱。

    > [!Note]  
    > 對於每個叢集，都應使用全新且唯一的主要設定檔 DNS 前置詞。

1. 選取 [Kubernetes 主要集區設定檔計數]。 此計數包含主要集區中的節點數目。 可能的值介於 1 到 7 之間。 此值應該是奇數。

1. 選取 [Kubernetes 主要 VM 的 VM 大小]。

1. 選取 [Kubernetes 節點集區設定檔計數]。 此計數包含叢集中的代理程式數目。 

1. 選取 [儲存體設定檔]。 您可以選擇 **Blob 磁碟**或**受控磁碟**。 這會指定 Kubernetes 節點 VM 的 VM 大小。 

1. 針對 Azure Stack 安裝的 **Azure Stack 身分識別系統**選取 [Azure AD]。 

1. 輸入**服務主體 ClientId**。Kubernetes Azure 雲端提供者會使用此識別碼。 當您建立服務主體時，用戶端識別碼已識別為應用程式識別碼。

1. 輸入您在建立服務主體時所建立的**服務主體用戶端密碼**。

1. 輸入 **Kubernetes Azure 雲端提供者版本**。 這是 Kubernetes Azure 提供者的版本。 Azure Stack 會為每個 Azure Stack 版本發行一個自訂 Kubernetes 組建。

### <a name="3-summary"></a>3.總結

1. 選取總結。 刀鋒視窗會顯示您 Kubernetes 叢集組態設定的驗證訊息。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 檢閱您的設定。

3. 選取 [確定] 以部署叢集。

> [!TIP]  
>  如果您有任何關於部署的問題，可以張貼您的問題，或查看是否有人已在 [Azure Stack 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中回答過此問題。


## <a name="next-steps"></a>後續步驟

[連線至您的叢集](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)