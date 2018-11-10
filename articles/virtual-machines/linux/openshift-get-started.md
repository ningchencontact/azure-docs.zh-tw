---
title: Azure 上的 OpenShift 概觀 | Microsoft Docs
description: Azure 中的 OpenShift 概觀。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: d68215359d50ac153d6df3bbcce5a9b6171698bb
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085434"
---
# <a name="openshift-in-azure"></a>Azure 中的 OpenShift

OpenShift 為企業帶來 Docker 和 Kubernetes 開放且可延伸的容器應用程式平台。  

OpenShift 包含容器協調流程與管理的 Kubernetes。 它新增以開發人員和以作業為中心的工具，可達成：

- 應用程式快速開發。
- 部署及調整更容易。
- 小組和應用程式的長期生命週期維護。

有多個版本的 OpenShift 可用：

- Red Hat OpenShift 容器平台
- OpenShift On Azure (CY2019 年度推出的完全受控 OpenShift)
- OKD (先前稱為 OpenShift Origin)
- OpenShift 專用
- OpenShift 線上

在本文涵蓋的五個版本中，目前只有兩個可供客戶部署至 Azure：OpenShift 容器平台和 OKD。

## <a name="openshift-container-platform"></a>Red Hat OpenShift 容器平台

「容器平台」是由 Red Hat 提供及支援、符合企業需求的[商業版](https://www.openshift.com)。 使用這個版本，客戶要購買 OpenShift 容器平台的必要權利，並負責安裝與管理整個基礎結構。

因為客戶「擁有」整個平台，因此他們可以將其安裝在內部部署資料中心或公用雲端 (如 Azure、AWS、Google)。

## <a name="openshift-on-azure"></a>OpenShift On Azure

OpenShift On Azure 是在 Azure 中執行的 OpenShift 完全受控供應項目。 這項服務是由 Microsoft 與 Red Hat 共同管理和支援。 叢集將會部署到客戶的 Azure 訂用帳戶中。 此服務目前為個人預覽版，已排定 CY 2019 年初公開上市。 我們將隨著供應項目愈來愈接近公開上市提供更多資訊。

## <a name="okd-formerly-openshift-origin"></a>OKD (先前稱為 OpenShift Origin)

OKD 是由社群支援的 OpenShift [開放原始碼](https://www.okd.io/)上游專案。 OKD 可以安裝在 CentOS 或 Red Hat Enterprise Linux (RHEL) 上。

## <a name="openshift-dedicated"></a>OpenShift 專用

「專用」是使用 OpenShift 容器平台並由 Red Hat 管理的*單一租用戶* OpenShift。 Red Hat 會管理所有的基礎結構 (VM、OpenShift 叢集、網路、儲存體等)。 叢集由一位客戶所特有，並在公用雲端 (如 AWS 或 Google 中執行。 初始叢集包含四個應用程式節點，且所有成本會每年先付款。

## <a name="openshift-online"></a>OpenShift 線上

「線上」使用容器平台，是由 Red Hat 管理的*多租用戶* OpenShift。 Red Hat 會管理所有的基礎結構 (例如 VM、OpenShift 叢集、網路、儲存體)。 

使用這個版本，客戶要部署容器，但是對於執行容器的主機沒有控制權。 因為「線上」為多租用戶，容器可能會和其他客戶的容器放在相同 VM 主機上。 成本按每個容器計算。

## <a name="next-steps"></a>後續步驟

- [在 Azure 中設定 OpenShift 的一般必要條件](./openshift-prerequisites.md)
- [在 Azure 中部署 OpenShift 容器平台](./openshift-container-platform.md)
- [在 Azure 中部署 OKD](./openshift-okd.md)
- [在 Azure Stack 中部署 OpenShift](./openshift-azure-stack.md)
- [部署後工作](./openshift-post-deployment.md)
- [針對 OpenShift 部署進行疑難排解](./openshift-troubleshooting.md)
