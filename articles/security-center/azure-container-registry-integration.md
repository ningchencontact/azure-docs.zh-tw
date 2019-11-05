---
title: Azure 資訊安全中心和 Azure Container Registry |Microsoft Docs
description: 瞭解 Azure 資訊安全中心與 Azure Container Registry 的整合
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f0de56f968488f0e5d551ad705cc6f8ca6e7bc47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521861"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry 與資訊安全中心整合（預覽）

Azure Container Registry （ACR）是受控的私用 Docker Registry 服務，可在中央登錄中儲存及管理 Azure 部署的容器映射。 它是以開放原始碼的 Docker Registry 2.0 為基礎。

搭配使用 ACR 與 Azure 資訊安全中心的標準層（請參閱[定價](security-center-pricing.md)）時，您可以更深入瞭解登錄和映射的弱點。

[Azure 資訊安全中心內的 ![Azure Container Registry （ACR）建議](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>整合的優點

資訊安全中心可識別您訂用帳戶中的 ACR 登錄，並順暢地提供：

* **Azure-原生弱點掃描**所有已推送的 Linux 映射。 資訊安全中心使用領先業界的弱點掃描廠商 Qualys 掃描影像。 這個原生解決方案預設會緊密整合。

* 具有已知弱點之 Linux 映射的**安全性建議**。 資訊安全中心提供每個回報的弱點和嚴重性分類的詳細資料。 此外，它也提供指引，說明如何補救每個推送至登錄的映射上找到的特定弱點。

![Azure 資訊安全中心和 Azure Container Registry （ACR）高階總覽](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>後續步驟

若要深入瞭解資訊安全中心的容器安全性功能，請參閱：

* [Azure 資訊安全中心和容器安全性](container-security.md)

* [與 Azure Kubernetes Service 整合](azure-kubernetes-service-integration.md)

* [虛擬機器保護](security-center-virtual-machine-protection.md)-描述資訊安全中心的建議