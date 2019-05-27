---
title: Azure Red Hat OpenShift 的常見問題集 |Microsoft Docs
description: 以下是有關 Microsoft Azure 的 Red Hat OpenShift 常見問題的解答
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 4f11eac106fe984aa06e41083c2d4f66c7d0e62c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872905"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure 的 Red Hat OpenShift 常見問題集

本文說明常見問題 (Faq) Microsoft Azure 的 Red Hat OpenShift。

## <a name="how-do-i-get-started"></a>如何開始使用？

您可以使用 Azure 的 Red Hat OpenShift 之前，您將需要購買至少 4 個 Azure 保留的 Red Hat OpenShift 的應用程式節點。

如果您是 Azure 的客戶[購買 Azure 保留的 Red Hat OpenShift 的執行個體](https://aka.ms/openshift/buy)透過 Azure 入口網站。 之後購買，您的訂用帳戶將會啟用在 24 小時內之後, 您將能夠佈建叢集。

如果您不是 Azure 的客戶[連絡銷售員](https://aka.ms/openshift/contact-sales)並填妥頁面底部的啟動程序的銷售表單。

請參閱[Azure 定價頁面上的 Red Hat OpenShift](https://aka.ms/openshift/pricing)如需詳細資訊。

## <a name="which-azure-regions-are-supported"></a>支援哪些 Azure 區域？

請參閱[支援資源](supported-resources.md#azure-regions)受到 Azure Red Hat OpenShift 的全域區域的清單。

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>可以部署到現有的虛擬網路的叢集嗎？

沒有。 但是，您可以現有的 VNET，透過對等互連來連線 Azure Red Hat OpenShift 叢集。 請參閱[叢集的虛擬網路連線到現有的虛擬網路](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network)如需詳細資訊。

## <a name="what-cluster-operations-are-available"></a>叢集作業可供使用？

此外，您只可以相應增加或減少計算節點數目。 若要允許任何其他修改`Microsoft.ContainerService/openShiftManagedClusters`之後建立的資源。 計算節點數目上限會限制為 20。

## <a name="what-virtual-machine-sizes-can-i-use"></a>可以使用哪些虛擬機器大小？

請參閱[Azure Red Hat OpenShift 的虛擬機器大小](supported-resources.md#virtual-machine-sizes)取得一份您可以使用與 Azure 的 Red Hat OpenShift 叢集的虛擬機器大小。

## <a name="is-data-on-my-cluster-encrypted"></a>是我加密的叢集上的資料？

根據預設，沒有待用加密。 Azure 儲存體平台會自動加密您的資料，再進行保存，且會在擷取之前的資料解密。 請參閱[待用資料的 Azure 儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)如需詳細資訊。

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>可以使用 Prometheus/Grafana 監視容器及管理容量嗎？

否，不是在目前的時間。

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>可從外部因此我可以使用 Jenkins 等工具時，才 Docker 登錄嗎？

Docker 登錄是可從`https://docker-registry.apps.<clustername>.<region>.azmosa.io/`不過，不會提供強式的儲存體的持久性保證。 您也可以使用[Azure Container Registry](https://azure.microsoft.com/services/container-registry/)。

## <a name="is-cross-namespace-networking-supported"></a>命名空間的跨網路是否支援？

客戶和個別的專案系統管理員可以根據每個專案使用自訂 （包括拒絕它） 的命名空間的跨網路`NetworkPolicy`物件。

## <a name="can-an-admin-manage-users-and-quotas"></a>系統管理員可以管理使用者和配額嗎？

是。 Azure Red Hat OpenShift 系統管理員可以管理使用者除了存取建立專案的所有使用者的配額。

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>可以限制叢集中只有特定的 Azure AD 使用者嗎？

是。 您可以限制哪些 Azure AD 使用者可以登入到叢集設定 Azure AD 應用程式。 如需詳細資訊，請參閱[How to:一組使用者限制您的應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>可以在叢集有多個 Azure 區域的計算節點嗎？

沒有。 Azure Red Hat OpenShift 叢集中所有節點必須都來自相同的 Azure 區域。

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>主要節點和基礎結構節點抽離不使用 Azure Kubernetes Service (AKS)？

沒有。 所有資源，包括叢集主機上，都執行您的客戶訂用帳戶中。 這些類型的資源會放在唯讀模式的資源群組。

## <a name="is-open-service-broker-for-azure-osba-supported"></a>是 Open Service Broker for Azure (OSBA) 支援？

是。 您可以使用 OSBA 與 Azure 的 Red Hat OpenShift。 請參閱[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template)如需詳細資訊。
