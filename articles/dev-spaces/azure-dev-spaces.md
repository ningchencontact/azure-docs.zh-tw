---
title: Azure 開發人員空間 | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器
manager: douge
ms.openlocfilehash: 14b51cc2ad2e8e0f294e5e73e542001e30d21c9d
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919873"
---
# <a name="azure-dev-spaces"></a>Azure 開發人員空間
Azure Dev Spaces 可為小組提供快速、疊代的 Kubernetes 開發經驗。 只需最基本的開發人員機器設定，您即可直接在 Azure Kubernetes Service (AKS) 中反覆執行和偵錯容器。 在 Windows、Mac 或 Linux 上使用熟悉的工具 (例如 Visual Studio、Visual Studio Code 或命令列) 進行開發。

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure 開發人員空間如何簡化 Kubernetes 開發 

Azure Dev Spaces 可透過下列方式協助開發小組在 Kubernetes 上更具生產力：
- 盡可能減少每個小組成員的本機開發人員機器設定，並直接在 Azure 中受管理的 Kubernetes 叢集 AKS 中工作。
- 使用 Visual Studio 2017 或 Visual Studio Code 直接在 Kubernetes 中快速地反覆執行和偵錯程式碼。
- 產生可讓您從開發到生產環境中一體適用的 Docker 和 Kubernetes 組態即程式碼資產。 
- 與您的小組共用受管理的 Kubernetes 叢集，並共同合作。 在隔離環境中開發您的程式碼，並對其他元件進行端對端測試，而無須複寫或模擬相依性。

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>另請參閱

[Azure Kubernetes Service](/azure/aks)