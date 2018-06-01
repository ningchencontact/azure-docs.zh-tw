---
title: Azure 開發人員空間 | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: 在 Azure 上使用容器和微服務快速進行 Kubernetes 開發
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198753"
---
# <a name="azure-dev-spaces"></a>Azure 開發人員空間
Azure 開發人員空間可協助您快速進行 Kubernetes 的開發。 透過 Azure 開發人員空間，您也可以新增完整的開發功能 (例如，對您的 Azure Kubernetes 容器進行偵錯)，並且可以使用熟悉的工具 (例如 VS Code、Visual Studio 或命令列) 在雲端中反覆地開發容器。 如果在進行小組開發時，個別的程式碼分支在開發週期內必須隔離於其本身的空間中，Azure 開發人員空間將更形重要。

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure 開發人員空間如何簡化 Kubernetes 開發 

此方法有幾項優點：

* 透過對雲端資源的完整存取，取得代表生產環境的無基礎結構開發環境。
* 透過 VS Code 或 Visual Studio，直接在 Kubernetes 中對 Node.js 和 .NET Core 容器進行偵錯。 所有其他語言皆可使用命令列介面進行開發。
* 在開發小組共用 Kubernetes 執行個體以節省成本，並且讓新的小組成員盡可能減少本機電腦設定。
* 在隔離環境中開發您的程式碼，並對其他元件進行端對端測試，而無須複寫或模擬相依性。

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)