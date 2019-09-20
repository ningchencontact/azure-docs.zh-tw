---
title: 在現有的可用性設定組中新增 Azure VM 的可支援性 | Microsoft Docs
description: 在現有的可用性設定組中新增 Azure VM 的可支援性。
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155448"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>在現有的可用性設定組中新增 Azure VM 的可支援性

當您將新的虛擬機器 (VM) 新增至現有的可用性設定組時，偶爾可能會遇到限制。 下表詳細列出您可以在同一個可用性設定組中混合的 VM 系列。

以下是混合不同 VM 類型的可支援性對照表：

系列與可用性設定組|第二部 VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|第一部 VM|||||||
|具有使用||[確定]|[確定]|[確定]|[確定]|[確定]|
|Av2||[確定]|[確定]|[確定]|[確定]|[確定]|
|D||[確定]|[確定]|[確定]|[確定]|[確定]|
|Dv2||[確定]|[確定]|[確定]|[確定]|[確定]|
|Dv3||[確定]|[確定]|[確定]|[確定]|[確定]|

其他所有系列由於需要特定硬體，因此無法存在於相同的可用性設定組。

因為專用 RDMA 後端網路的需求，所以無法混合 A8/A9 VM 大小。
