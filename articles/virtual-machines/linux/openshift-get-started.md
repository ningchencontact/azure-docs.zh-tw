---
title: Azure 上的 OpenShift 概觀 | Microsoft Docs
description: Azure 中的 OpenShift 概觀。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 53bed2131e81ee5ed0f46bde389262ee8349339a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542419"
---
# <a name="openshift-in-azure"></a>Azure 中的 OpenShift

OpenShift 為企業帶來 Docker 和 Kubernetes 開放且可延伸的容器應用程式平台。  

OpenShift 包含容器協調流程與管理的 Kubernetes。 它新增以開發人員和以作業為中心的工具，可達成：

- 應用程式快速開發。
- 部署及調整更容易。
- 小組和應用程式的長期生命週期維護。

沒有可用的 OpenShift 的多個版本。  這些版本中，只有兩個可立即供客戶在 Azure 中部署：OpenShift 容器平台和 OKD (先前稱為 OpenShift Origin)。

## <a name="openshift-container-platform"></a>Red Hat OpenShift 容器平台

「容器平台」是由 Red Hat 提供及支援、符合企業需求的[商業版](https://www.openshift.com)。 使用這個版本，客戶要購買 OpenShift 容器平台的必要權利，並負責安裝與管理整個基礎結構。

因為客戶 「 擁有 」 整個平台，他們可以在內部部署資料中心，或安裝在公用雲端 （例如 Azure)。

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Azure 的 Red Hat OpenShift 是完全受控的供應項目，在 Azure 中執行的 OpenShift。 這項服務是由 Microsoft 與 Red Hat 共同管理和支援。 叢集將會部署到客戶的 Azure 訂用帳戶中。 服務被預定在 2019 年周圍的 GA。 個別受管理的服務文件時，即可使用此服務為 ga 狀態。

## <a name="okd"></a>OKD

OKD 是由社群支援的 OpenShift [開放原始碼](https://www.okd.io/)上游專案。 OKD 可以安裝在 CentOS 或 Red Hat Enterprise Linux (RHEL) 上。

## <a name="next-steps"></a>後續步驟

- [在 Azure 中設定 OpenShift 的一般必要條件](./openshift-prerequisites.md)
- [在 Azure 中部署 OpenShift 容器平台](./openshift-container-platform.md)
- [部署 OpenShift 容器平台自我管理的 Marketplace 供應項目](./openshift-marketplace-self-managed.md)
- [在 Azure Stack 中部署 OpenShift](./openshift-azure-stack.md)
- [部署後工作](./openshift-post-deployment.md)
- [針對 OpenShift 部署進行疑難排解](./openshift-troubleshooting.md)
