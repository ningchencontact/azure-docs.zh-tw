---
title: Azure Red Hat OpenShift 的支援資源 |Microsoft Docs
description: 了解 Microsoft Azure 的 Red Hat OpenShift 支援哪些 Azure 區域與虛擬機器大小。
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 22cc195b7808ad4a9111aafcf883a68c51f3709f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076101"
---
# <a name="azure-red-hat-openshift-resources"></a>Red Hat OpenShift 的 azure 資源

本主題列出 Azure 區域和 Microsoft Azure 的 Red Hat OpenShift 服務所支援的虛擬機器大小。

## <a name="azure-regions"></a>Azure 區域

您可以將 Azure Red Hat OpenShift 叢集部署到在世界各地的下列 Azure 區域：

|區域|CLI 程式碼|
|-|-|
|🇦🇺 澳大利亞東部|`australiaeast`|
|🇨🇦 加拿大中部|`canadacentral`|
|🇨🇦 加拿大東部|`canadaeast`|
|🇺🇸 美國東部|`eastus`|
|🇺🇸 美國西部|`westus`|
|🇪🇺 西歐|`westeurope`|
|🇪🇺 北歐|`northeurope`|

## <a name="virtual-machine-sizes"></a>虛擬機器大小

以下是您可以指定運算節點，在您的 Azure Red Hat OpenShift 叢集支援的虛擬機器大小。

> [!Important]
> 每個 VM 有不同數目的可附加的磁碟機。 這可能不是立即清除記憶體或 CPU 的大小。
> 並非所有 VM 大小在所有區域都可供使用。 即使此 API 支援您指定的大小，您可能會發生錯誤，如果您指定的區域中無法使用大小。
> 請參閱[每個區域的目前清單的 支援的 VM 大小](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)如需詳細資訊。

## <a name="compute-node-sizes"></a>計算節點大小

Azure Red Hat OpenShift 的 REST API 支援下列的計算節點大小：

|大小|vCPU|RAM|
|-|-|-|
|標準 D4s v3 系列|4|16 GB|
|標準 D8s v3 系列|8|32 GB|
|標準 D16s v3 系列|16|64 GB|
|標準 D32s v3 系列|32|128 GB|
|-|-|-|
|標準 E4s v3 系列|4|32 GB|
|標準 E8s v3 系列|8|64 GB|
|標準 E16s v3 系列|16|128 GB|
|標準 E32s v3 系列|32|256 GB|
|-|-|-|
|標準 F8s v2|8|16 GB|
|標準 F16s v2|16|32 GB|
|標準 F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>主要節點大小

下列的主要 / 基礎結構節點大小會受到 Azure Red Hat OpenShift 的 REST API:

|大小|vCPU|RAM|
|-|-|-|
|標準 D4s v3 系列|4|16 GB|
|標準 D8s v3 系列|8|32 GB|
|標準 D16s v3 系列|16|64 GB|
|標準 D32s v3 系列|32|128 GB|

## <a name="next-steps"></a>後續步驟

請嘗試[建立 Azure 的 Red Hat OpenShift 叢集](tutorial-create-cluster.md)教學課程。