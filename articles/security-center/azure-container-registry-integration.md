---
title: Azure 資訊安全中心和 Azure Container Registry
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
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 4cc88e7c04d10907a9a6386b1266eb8031d60926
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552673"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry 與資訊安全中心整合（預覽）

Azure Container Registry （ACR）是受控的私用 Docker Registry 服務，可在中央登錄中儲存及管理 Azure 部署的容器映射。 它是以開放原始碼的 Docker Registry 2.0 為基礎。

若要深入瞭解您的登錄和映射弱點，Azure 資訊安全中心的標準層使用者可以啟用選用的容器登錄套件組合。 使用這項功能的成本會依每個影像計費，而不是依掃描收費。 如需詳細資訊，請參閱[定價](security-center-pricing.md)。 啟用配套時，資訊安全中心會在每次將映射推送至登錄時，自動掃描您註冊中的映射。

> [!NOTE]
> 只有在啟用容器登錄套件組合並將映射推送至登錄之後，才會對登錄進行資訊安全中心的第一次掃描。

當掃描完成時（通常在大約10分鐘之後），資訊安全中心建議中會提供結果，如下所示：

[![範例 Azure 資訊安全中心有關在 Azure Container Registry （ACR）主控映射中探索到的弱點的建議](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

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
