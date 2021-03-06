---
title: 將 Kubernetes 叢集新增至 Azure Stack Marketplace | Microsoft Docs
description: 了解如何將 Kubernetes 叢集新增至 Azure Stack Marketplace。
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: ed2d55b8346acb79563a882bbaf2f46110dcf1bb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442712"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>將 Kubernetes 叢集新增至 Azure Stack Marketplace

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

> [!note]  
> Azure Stack 上的 Azure Container Service (ACS) Kubernetes 目前處於預覽狀態。 若要要求執行本文中指示所需 Kubernetes Marketplace 項目的存取權，請[提交要求以取得存取權](https://aka.ms/azsk8)。

您能以 Marketplace 項目的方式向使用者提供 Kubernetes 叢集。 您的使用者可以在單一協調作業中部署 Kubernetes。

以下文章說明如何使用 Azure Resource Manager 範本來部署及佈建獨立 Kubernetes 叢集的資源。 開始之前，請檢查您的 Azure Stack 和全域的 Azure 租用戶設定。 收集有關您 Azure Stack 的必要資訊。 將必要的資源新增到您的租用戶及 Azure Stack Marketplace。 叢集仰賴 Ubuntu 伺服器、自訂指令碼和會在 Marketplace 中的 Kubernetes 叢集項目。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>建立方案、供應項目和訂用帳戶

建立 Kubernetes Cluster Marketplace 項目的方案、供應項目和訂用帳戶。 您也可以使用現有的方案和供應項目。

1. 登入[系統管理入口網站](https://adminportal.local.azurestack.external)。

1. 建立一個方案作為基本方案。 如需指示，請參閱[在 Azure Stack 中建立方案](azure-stack-create-plan.md)。

1. 建立供應項目。 如需指示，請參閱[在 Azure Stack 中建立供應項目](azure-stack-create-offer.md)。

1. 選取 [供應項目]，並尋找您所建立的供應項目。

1. 在 [供應項目] 刀鋒視窗中選取 [概觀]。

1. 選取 [變更狀態]。 選取 [公用]。

1. 選取 [+ 新增] > [供應項目與方案] > [訂用帳戶] 來建立新的訂用帳戶。

    a. 輸入 [顯示名稱]。

    b. 輸入 [使用者]。 使用與您的租用戶相關聯的 Azure AD 帳戶。

    c. [提供者描述]

    d. 將 [目錄租用戶] 設定成 Azure Stack 的 Azure AD 租用戶。 

    e. 選取 [供應項目]。 選取您所建立供應項目的名稱。 記下訂用帳戶識別碼。

## <a name="add-an-ubuntu-server-image"></a>新增 Ubuntu 伺服器映像

將以下 Ubuntu Server 映像新增到 Marketplace：

1. 登入[系統管理入口網站](https://adminportal.local.azurestack.external)。

1. 選取 [更多服務] > [Marketplace 管理]。

1. 選取 [+ 從 Azure 加入]。

1. 輸入 `UbuntuServer`。

1. 選取具有下列設定檔的伺服器：
    - **發行者**：Canonical
    - **供應項目**：UbuntuServer
    - **SKU**：16.04-LTS
    - **版本**：16.04.201802220

    > [!Note]  
    > 列出的 Ubuntu Server 16.04 LTS 版本可能會超過一個。 您必須新增完全符合的版本。 Kubernetes 叢集需要項目的正確版本。

1. 選取 [下載]。

## <a name="add-a-custom-script-for-linux"></a>新增 Custom Script for Linux

從 Marketplace 新增 Kubernetes 叢集：

1. 開啟[系統管理入口網站](https://adminportal.local.azurestack.external)。

1. 選取 [更多服務] > [Marketplace 管理]。

1. 選取 [+ 從 Azure 加入]。

1. 輸入 `Custom Script for Linux`。

1. 選取具有下列設定檔的指令碼：
    - **供應項目**：Custom Script for Linux 2.0
    - **版本**：2.0.3
    - **發行者**：Microsoft Corp

    > [!Note]  
    > 列出的 Custom Script for Linux 版本可能會超過一個。 您必須新增完全符合的版本。 Kubernetes 叢集需要項目的正確版本。

1. 選取 [下載]。


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>將 Kubernetes 叢集新增至 Marketplace

1. 開啟[系統管理入口網站](https://adminportal.local.azurestack.external)。

1. 選取 [更多服務] > [Marketplace 管理]。

1. 選取 [+ 從 Azure 加入]。

1. 輸入 `Kubernetes Cluster`。

1. 選取 `Kubernetes Cluster`。

1. 選取 [下載]。

    > [!note]  
    > 可能需要五分鐘，Marketplace 項目才會出現在 Marketplace 中。

    ![Kubernetes 叢集](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>更新或移除 Kubernetes 叢集 

在更新 Kubernetes 叢集項目時，您必須將 Marketplace 中的項目移除。 接著，您可以遵循此文章的指示，將 Kubernetes 叢集新增到 Marketplace。

移除 Kubernetes 叢集項目：

1. 請記下目前項目的名稱，例如 `Microsoft.AzureStackKubernetesCluster.0.1.0`

1. 使用 PowerShell 連線到 Azure Stack。

1. 使用以下 PowerShell Cmdlet 來移除項目：

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>後續步驟

[將 Kubernetes 叢集部署至 Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)




  [Azure Stack 中的服務提供概觀](azure-stack-offer-services-overview.md)