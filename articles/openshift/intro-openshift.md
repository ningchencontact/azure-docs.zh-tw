---
title: Azure Red Hat OpenShift 簡介 | Microsoft Docs
description: 了解 Microsoft Azure Red Hat OpenShift 的功能與優點，以部署及管理容器型應用程式。
services: container-service
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: 6121c0f654a61a147e84f0697f3ddb06b7c5db92
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079946"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft *Azure Red Hat OpenShift* 服務可讓您部署完全受控的 [OpenShift](https://www.openshift.com/) 叢集。

Azure Red Hat OpenShift 可擴充 [Kubernetes](https://kubernetes.io/)。 要在使用 Kubernetes 的生產環境中執行容器，需要其他工具和資源，例如映像登錄、儲存體管理、網路解決方案，以及記錄和監視工具，而這些項目必須受到一體的版本管理和測試。 要建置容器型應用程式，則需進一步進行與中介軟體、架構、資料庫和 CI/CD 工具的整合工作。 Azure Red Hat OpenShift 將其全數結合到單一平台中，不僅讓 IT 小組的作業更為簡便，同時也為應用程式小組提供他們必須執行的功能。

Azure Red Hat OpenShift 由 Red Hat 與 Microsoft 共同設計、運作和支援，以提供整合的支援體驗。 無須執行任何虛擬機器，也不需要修補。 Microsoft 和 Red Hat 會為您修補、更新及監控主要節點、基礎結構和應用程式節點。 Azure Red Hat OpenShift 叢集會部署到您的 Azure 訂用帳戶中，並且包含在您的 Azure 帳單上。

您可以自行選擇登錄、網路功能、儲存體和 CI/CD 解決方案，或使用內建解決方案來進行自動化原始程式碼管理、容器和應用程式建置、部署、調整、健康情況管理等功能。 Azure Red Hat OpenShift 可透過 Azure Active Directory 提供整合式登入體驗。

若要開始使用，請完成[建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。

## <a name="access-security-and-monitoring"></a>存取、安全性和監視

為了提升安全性和管理能力，Azure Red Hat OpenShift 可讓您與 Azure Active Directory (Azure AD) 整合，並使用 Kubernetes 角色型存取控制 (RBAC)。 您也可以監視叢集與資源的健康情況。

## <a name="cluster-and-node"></a>叢集與節點

Azure Red Hat OpenShift 節點執行於 Azure 虛擬機器上。 您可以將儲存體連接到節點和 Pod、升級叢集節點，以及使用 GPU。

## <a name="virtual-networks-and-ingress"></a>虛擬網路與輸入

您可以將 Azure Red Hat OpenShift 叢集部署到現有的虛擬網路中。 在此設定中，叢集中的每個 Pod 都會獲指派虛擬網路中的 IP 位址，而且可以直接與叢集中的其他 Pod 以及虛擬網路中的其他節點通訊。 Pod 也可以連線到對等互連虛擬網路中的其他服務，也可以透過 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) 或站對站 (S2S) VPN 連線來連線到內部部署網路。

如需詳細資訊，請參閱[ 在 Azure 上建立 Microsoft Red Hat OpenShift 叢集](tutorial-create-cluster.md)。

## <a name="kubernetes-certification"></a>Kubernetes 憑證

Azure Red Hat OpenShift 服務已經由 CNCF 認證，符合 Kubernetes 相關規範。

## <a name="next-steps"></a>後續步驟

了解 Azure Red Hat OpenShift 的必要條件：

> [!div class="nextstepaction"]
> [設定開發環境](howto-setup-environment.md)