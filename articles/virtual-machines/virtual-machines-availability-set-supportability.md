---
title: 在現有的可用性設定組中新增 Azure VM 的可支援性 | Microsoft Docs
description: 在現有的可用性設定組中新增 Azure VM 的可支援性。
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 7a5e97b66fec040b4ec32caa8d58cf9b50169a33
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60443699"
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

A8/A9 VM 大小不能混合因為專用 RDMA 後端網路上的需求。
