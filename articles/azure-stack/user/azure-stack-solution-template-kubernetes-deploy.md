---
title: 將 Kubernetes 叢集部署至 Azure Stack | Microsoft Docs
description: 了解如何將 Kubernetes 叢集部署至 Azure Stack。
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
ms.date: 08/09/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: f6a2b3f242e5989d0c72083eef4faad9c4798cfe
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2018
ms.locfileid: "41954838"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>將 Kubernetes 叢集部署至 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

> [!Note]  
> Azure Stack 上的 Azure Kubernetes Services (AKS) Kubernetes 目前為個人預覽版。 要執行本文中指示的作業，您的 Azure Stack 操作員必須要求對 Kubernetes Marketplace 項目的存取權。

以下文章說明如何使用 Azure Resource Manager 解決方案範本，在單一協調作業中部署和佈建 Kubernetes 的資源。 您將必須收集 Azure Stack 安裝所需的相關資訊、產生範本，然後部署至您的雲端。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

您可以在 Azure Stack 上，使用 Azure Kubernetes Services (AKS) 引擎所產生的 Azure Resource Manager 範本來安裝 Kubernetes。 [Kubernetes](https://kubernetes.io) 是一種開放原始碼系統，可進行自動化部署、調整和管理容器中的應用程式。 [容器](https://www.docker.com/what-container)包含類似於 VM 的映像中。 不同於 VM，容器映像只會包含其執行應用程式所需的資源，例如程式碼、執行程式碼的執行階段、特定程式庫和設定等。

使用 Kubernetes 可執行下列作業：

- 開發可大幅調整、可升級，並且可在短短數秒內完成部署的應用程式。 
- 簡化您的應用程式設計，並透過不同的 Helm 應用程式改善其可靠性。 [Helm](https://github.com/kubernetes/helm) 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。
- 透過調整與升級功能輕鬆監視和診斷應用程式的健康情況。

## <a name="prerequisites"></a>必要條件 

若要開始使用，請確定您具有適當權限，並且已備妥 Azure Stack。

1. 確認您可以在 Azure Active Directory (Azure AD) 租用戶中建立應用程式。 您必須具備這些權限才能進行 Kubernetes 部署。

    如需檢查權限的指示，請參閱[檢查 Azure Active Directory 權限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)。

1. 產生在 Azure Stack 上用來登入 Linux VM 的 SSH 公開和私密金鑰組。 您在建立叢集時將需要用到公開金鑰。

    如需產生金鑰的指示，請參閱 [SSH 金鑰產生](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)。

1. 確認您在 Azure Stack 租用戶入口網站中具有有效的訂用帳戶，且您有足夠的公用 IP 位址可用來新增應用程式。

    叢集無法部署至 Azure Stack 的**系統管理員**訂用帳戶。 您必須使用「使用者」訂用帳戶。 

## <a name="create-a-service-principal-in-azure-ad"></a>在 Azure AD 中建立服務主體

1. 登入全域 [Azure 入口網站](http://portal.azure.com)。
1. 確認您已使用與 Azure Stack 執行個體相關聯的 Azure AD 租用戶登入。
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

1.  登入[系統管理入口網站](https://adminportal.local.azurestack.external)。

1. 選取 [更多服務] > [使用者訂用帳戶] > [+ 新增]。

1. 選取您所建立的訂用帳戶。

1. 選取 [存取控制 (IAM)] > 選取 [+ 新增]。

1. 選取 [擁有者] 角色。

1. 選取為您的服務主體建立的應用程式名稱。 您可能需要在搜尋方塊中輸入名稱。

1. 按一下 [檔案] 。

## <a name="deploy-a-kubernetes-cluster"></a>部署 Kubernetes 叢集

1. 開啟 [Azure Stack 入口網站](https://portal.local.azurestack.external)。

1. 選取 [+ 新增] > [計算] > [Kubernetes 叢集]。 按一下頁面底部的 [新增] 。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

1. 在 [建立 Kubernetes 叢集] 中選取 [基本資料]。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 輸入 **Linux VM 系統管理員使用者名稱**。 這是屬於 Kubernetes 叢集和 DVM 的 Linux 虛擬機器的使用者名稱。

1. 輸入用來對建立為 Kubernetes 叢集和 DVM 一部分的所有 Linux 機器進行授權的 **SSH 公開金鑰**。

1. 輸入在區域中具有唯一性的**主要設定檔 DNS 前置詞**。 這必須是區域中唯一的名稱，例如 `k8s-12345`。 根據最佳做法，請試著選擇與資源群組名稱相同的名稱。

    > [!Note]  
    > 對於每個叢集，都應使用全新且唯一的主要設定檔 DNS 前置詞。

1. 輸入**代理程式集區設定檔計數**。 此計數包含叢集中的代理程式數目。 可能的值介於 1 到 4 之間。

1. 輸入**服務主體 ClientId**。Kubernetes Azure 雲端提供者會使用此識別碼。

1. 輸入您在建立服務主體應用程式時所建立的**服務主體用戶端密碼**。

1. 輸入 **Kubernetes Azure 雲端提供者版本**。 這是 Kubernetes Azure 提供者的版本。 Azure Stack 會為每個 Azure Stack 版本發行一個自訂 Kubernetes 組建。

1. 選取**訂用帳戶**識別碼。

1. 輸入新資源群組的名稱，或選取現有的資源群組。 資源名稱必須是小寫的英數字元。

1. 選取資源群組的 [位置]。 這是您選擇用來安裝 Azure Stack 的區域。

### <a name="specify-the-azure-stack-settings"></a>指定 Azure Stack 設定

1. 選取 [Azure Stack 戳記設定]。

    ![部署解決方案範本](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. 輸入**租用戶 ARM 端點**。 這是要連線以建立 Kubernetes 叢集之資源群組的 Azure Resource Manager 端點。 您必須向 Azure Stack 操作員取得整合系統的端點。 如需 Azure Stack 開發套件 (ASDK)，您可以使用 `https://management.local.azurestack.external`。

1. 輸入租用戶的**租用戶識別碼**。 如果您需要相關尋找此值的說明，請參閱[取得租用戶識別碼](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)。 

## <a name="connect-to-your-cluster"></a>連線至您的叢集

您現在已可連線至您的叢集。 您可以在叢集資源群組中找到主要叢集，其名稱為 `k8s-master-<sequence-of-numbers>`。 使用 SSH 用戶端來連線至主要叢集。 在主要叢集上，您可以使用 **kubectl**，這是用來管理叢集的 Kubernetes 命令列用戶端。 如需指示，請參閱 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

您也可能會發現 **Helm** 套件管理員很適合用來對叢集安裝和部署應用程式。 如需對叢集安裝和使用 Helm 的指示，請參閱 [helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>後續步驟

[將 Kubernetes 叢集新增至 Marketplace (適用於 Azure Stack 操作員)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
