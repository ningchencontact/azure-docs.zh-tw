---
title: 介紹 Azure Kubernetes Service
description: Azure Kubernetes Service 可讓您輕鬆地部署和管理 Azure 上的容器型應用程式。
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4b2294d7816a92dccb14caaadc09a2797edeafe6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36237375"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) 可讓您輕鬆地在 Azure 中部署受控 Kubernetes 叢集。 AKS 可降低管理 Kubernetes 的複雜性和作業負荷，因為是由 Azure 負責大部分的工作。 以主控的 Kubernetes 服務形式，Azure 會為您處理像是健康狀態監視和維護等重要工作。 此外，此服務是免費的，您僅需支付叢集內代理程式節點的費用，不需支付主機費用。

本文件提供Azure Kubernetes Service (AKS) 的功能概觀。

## <a name="flexible-deployment-options"></a>彈性的部署選項

Azure Kubernetes Service 提供入口網站、命令列和範本驅動部署選項 (Resource Manager 範本和 Terraform)。 部署 AKS 叢集時，系統會為您部署和設定 Kubernetes 主機和所有節點。 部署程序中也可以設定其他功能，例如進階網路功能、Azure Active Directory 整合及監視功能。

如需詳細資訊，請參閱 [AKS 入口網站快速入門][aks-portal]或 [AKS CLI 快速入門][aks-cli]。

## <a name="identity-and-security-management"></a>身分識別和安全性管理

AKS 叢集支援[角色型存取控制 (RBAC)][kubernetes-rbac]。 AKS 叢集也可設定為與 Azure Active Directory 整合。 在此設定中，您可以根據 Azure Active Directory 身分識別和群組成員資格來設定 Kubernetes 的存取權。

如需詳細資訊，請參閱[整合 Azure Active Directory 與 AKS][aks-aad]。

## <a name="integrated-logging-and-monitoring"></a>整合的記錄和監視功能

容器健康情況可透過從容器、節點及控制器收集記憶體與處理器計量，讓您看到效能狀況。 容器記錄也會一併收集。 這項資料會儲存在您的 Log Analytics 工作區，而且可透過 Azure 入口網站、Azure CLI 或 REST 端點取得。

如需詳細資訊，請參閱[監視 Azure Kubernetes Service 的容器健康情況][container-health]。

## <a name="cluster-node-scaling"></a>叢集節點縮放

當資源的需求增加時，您可以相應放大 AKS 叢集的節點來因應。 如果資源的需求減少時，您可以藉由相應縮小叢集來移除節點。 AKS 縮放作業可以透過 Azure 入口網站或 Azure CLI 來完成。

如需詳細資訊，請參閱[縮放 Azure Kubernetes Service (AKS) 叢集][aks-scale]。

## <a name="cluster-node-upgrades"></a>叢集節點升級

Azure Kubernetes Service 提供多個 Kubernetes 版本。 當 AKS 有新版本可用時，您可以使用 Azure 入口網站或 Azure CLI 來升級您的叢集。 在升級過程中，會將節點仔細地隔離並清空，將中斷執行中應用程式的情況降到最低。

如需詳細資訊，請參閱[升級 Azure Kubernetes Service (AKS) 叢集][aks-upgrade]。

## <a name="http-application-routing"></a>HTTP 應用程式路由

HTTP 應用程式路由解決方案可讓您輕鬆存取已部署至 AKS 叢集的應用程式。 啟用時，HTTP 應用程式路由解決方案會設定 AKS 叢集中的輸入控制器。 部署應用程式時，系統會自動設定可公開存取的 DNS 名稱。

如需詳細資訊，請參閱 [HTTP 應用程式路由][aks-http-routing]。

## <a name="gpu-enabled-nodes"></a>已啟用 GPU 的節點

AKS 支援建立已啟用 GPU 的節點集區。 Azure 目前提供單一或多個已啟用 GPU 的虛擬機器。 已啟用 GPU 的虛擬機器是專門針對計算密集型、圖形密集型及視覺效果的工作負載所設計。

如需詳細資訊，請參閱[在 AKS 上使用 GPU][aks-gpu]。

## <a name="development-tooling-integration"></a>開發工具整合

Kubernetes 具有豐富的開發和管理工具生態系統，例如 Helm、Draft 和 Visual Studio Code 的 Kubernetes 擴充功能。 這些工具可流暢地與 Azure Kuberntees Service 搭配使用。

此外，Azure Dev Spaces 可為小組提供快速、疊代的 Kubernetes 開發經驗。 只需最基本設定，您即可直接在 Azure Kubernetes Service (AKS) 中執行和偵錯容器。

如需詳細資訊，請參閱 [Azure Dev Spaces][azure-dev-spaces]。

Azure DevOps 專案提供簡單的解決方案，讓您可以將現有的程式碼和 Git 存放庫帶入 Azure。 DevOps 專案會自動建立 Azure 資源 (AKS) 和 VSTS 中的發行管線 ( VSTS 中包含 CI 的組建定義)，也會設定 CD 的發行定義，然後建立用於監視的 Azure Application Insights 資源。

如需詳細資訊，請參閱 [Azure DevOps 專案][azure-devops]。

## <a name="virtual-network-integration"></a>虛擬網路整合

AKS 叢集可以部署到現有的 VNet 中。 在此設定中，叢集中的每個 Pod 都會獲派 VNet 中的 IP 位址，並可直接與叢集中的其他 Pod 以及 VNet 中的其他節點通訊。 Pod 也可以連線至對等互連 VNet 中的其他服務，也可以透過 ExpressRoute 和站對站 (S2S) VPN 連線來連線至內部部署網路。

如需詳細資訊，請參閱 [AKS 網路功能概觀][aks-networking]。

## <a name="private-container-registry"></a>Azure 容器登錄

針對您 Docker 映像的私人儲存體，整合 Azure Container Registry (ACR)。

如需詳細資訊，請參閱[Azure Container Registry (ACR)][acr-docs]。

## <a name="storage-volume-support"></a>存放磁碟區支援

Azure Kubernetes Service (AKS) 支援裝載永續性資料的存放磁碟區。 AKS 叢集會在建立時包含 Azure 檔案服務和 Azure 磁碟支援。

如需詳細資訊，請參閱 [Azure 檔案服務][azure-files]和 [Azure 磁碟][azure-disk]。

## <a name="docker-image-support"></a>Docker 映像支援

Azure Kubernetes Service (AKS) 支援 Docker 映像格式。

## <a name="kubernetes-certification"></a>Kubernetes 憑證

Azure Kubernetes Service (AKS) 已經由 CNCF 認證，符合 Kubernetes 相關規範。

## <a name="regulatory-compliance"></a>法規遵循

Azure Kubernetes Service (AKS) 符合 SOC 和 ISO/HIPPA/HITRUST 的規範。

## <a name="next-steps"></a>後續步驟

使用 AKS 快速入門深入了解部署和管理 AKS。

> [!div class="nextstepaction"]
> [AKS 快速入門][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/vsts/pipelines/actions/azure-devops-project-aks?view=vsts
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

