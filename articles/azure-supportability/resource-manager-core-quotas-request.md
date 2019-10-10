---
title: Azure Resource Manager vCPU 配額增加要求 | Microsoft Docs
description: Azure Resource Manager vCPU 配額增加要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248661"
---
# <a name="quota-increase-requests"></a>增加配額要求

針對每個區域中的每個訂用帳戶，會在兩個層級強制執行虛擬機器和虛擬機器擴展集的 Resource Manager vCPU 配額。 

第一層是總區域個 vcpu 限制（跨所有 VM 系列），而第二層是每個 VM 系列個 vcpu 限制（例如 D 系列個 vcpu）。 每當部署新的 VM 時，該 VM 系列新的和現有個 vcpu 使用量的總和，不得超過該特定 VM 系列所核准的 vCPU 配額。 此外，在所有 VM 系列上部署的新的和現有的 vCPU 計數，不應超過訂用帳戶所核准的區域個 vcpu 配額總計。 如果超過這些配額，將不允許 VM 部署。
您可以從 Azure 入口網站為 VM 系列要求增加個 vcpu 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域個 vcpu 限制。 

建立新的訂用帳戶時，預設的 [區域個 vcpu 總計] 可能不等於所有個別 VM 系列的預設 vCPU 配額總和。 這可能會導致您想要部署的每個個別 VM 系列的訂用帳戶具有足夠的配額，但不會有足夠的配額可用於所有部署的總區域個 vcpu。 在此情況下，您將需要提交要求，以明確地增加區域個 vcpu 限制的總計。 區域個 vcpu 限制總計不得超過該區域所有 VM 系列的核准配額總和。

深入瞭解 [[虛擬機器 vCPU 配額] 頁面](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和 [ [Azure 訂用帳戶和服務限制](https://aka.ms/quotalimits)] 頁面上的配額。 

