---
title: 部署 Kubernetes 以使用 Azure Stack 容器 | Microsoft Docs
description: 了解如何部署 Kubernetes 以使用 Azure Stack 提供的容器。
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
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 77275ec274a9c76918874007cfe564eea09e6de5
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877188"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>部署 Kubernetes 以使用 Azure Stack 提供的容器

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

> [!Note]  
> Azure Stack 上的 Kubernetes 處於預覽狀態。 預覽版目前不支援 Azure Stack 中斷連線的案例。

您可以遵循本文中的步驟，在單一協調作業中部署及設定 Kubernetes 的資源。 這些步驟會使用 Azure Resource Manager 解決方案範本。 您必須收集 Azure Stack 安裝所需的相關資訊、產生範本，然後部署至您的雲端。 Azure Stack 範本不會使用全域 Azure 中提供的相同受控 AKS 服務。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

您可以在 Azure Stack 上，使用ACS 引擎產生的 Azure Resource Manager 範本來安裝 Kubernetes。 [Kubernetes](https://kubernetes.io) 是一種開放原始碼系統，可進行自動化部署、調整和管理容器中的應用程式。 [容器](https://www.docker.com/what-container)位於映像中。 此容器映像類似於 VM，但與 VM 不同的是，容器只包含其執行應用程式所需的資源，例如程式碼、執行程式碼的執行階段、特定程式庫和設定等。

使用 Kubernetes 可執行下列作業：

- 開發可大幅調整、可升級，並且可在短短數秒內完成部署的應用程式。 
- 簡化您的應用程式設計，並透過不同的 Helm 應用程式改善其可靠性。 [Helm](https://github.com/kubernetes/helm) 是開放原始碼的封裝工具，可協助您安裝和管理 Kubernetes 應用程式的生命週期。
- 輕鬆監視和診斷應用程式的健康情況。

您只須支付支援您叢集節點所需的計算使用量。 如需詳細資訊，請參閱 [Azure Stack 中的使用量和計費](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback)。

## <a name="deploy-kubernetes-to-use-containers"></a>部署 Kubernetes 以使用容器

在 Azure Stack 上部署 Kubernetes 叢集的步驟取決於您的身分識別管理服務。 確認您的 Azure Stack 安裝所使用的身分識別管理解決方案。 請連絡 Azure Stack 系統管理員來確認您的身分識別管理服務。

- **Azure Active Directory (Azure AD)**  
如需使用 Azure AD 時安裝叢集的指示，請參閱[使用 Azure Active Directory (Azure AD) 將 Kubernetes 部署至 Azure Stack](azure-stack-solution-template-kubernetes-azuread.md)。

- **Active Directory 同盟服務 (AD FS)**  
如需使用 AD FS 時安裝叢集的指示，請參閱[使用 Active Directory 同盟服務 (AD FS) 將 Kubernetes 部署至 Azure Stack](azure-stack-solution-template-kubernetes-adfs.md)。

## <a name="connect-to-your-cluster"></a>連線至您的叢集

您現在已可連線至您的叢集。 您可以在叢集資源群組中找到主要叢集，其名稱為 `k8s-master-<sequence-of-numbers>`。 使用 SSH 用戶端來連線至主要叢集。 在主要叢集上，您可以使用 **kubectl**，這是用來管理叢集的 Kubernetes 命令列用戶端。 如需指示，請參閱 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

您也可能會發現 **Helm** 套件管理員很適合用來對叢集安裝和部署應用程式。 如需對叢集安裝和使用 Helm 的指示，請參閱 [helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>後續步驟

[啟用 Kubernetes 儀表板](azure-stack-solution-template-kubernetes-dashboard.md)

[將 Kubernetes 新增至 Marketplace (適用於 Azure Stack 操作員)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[使用 Azure Active Directory (Azure AD) 將 Kubernetes 部署至 Azure Stack](azure-stack-solution-template-kubernetes-azuread.md)

[使用 Active Directory 同盟服務 (AD FS) 將 Kubernetes 部署至 Azure Stack](azure-stack-solution-template-kubernetes-adfs.md)

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
