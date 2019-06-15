---
title: Azure Resource Manager vCPU 配額增加要求 | Microsoft Docs
description: Azure Resource Manager vCPU 配額增加要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9a997af984b92ea59cc02d99fbd66d8967ca31bd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076818"
---
# <a name="quota-increase-requests"></a>增加配額要求

每個訂用帳戶，每個區域中的兩個層級強制執行的虛擬機器和虛擬機器擴展集的 resource Manager vCPU 配額。 

第一層的總區域的 Vcpu 限制 （跨所有 VM 系列），而第二層每個 VM 系列 Vcpu 限制 （例如 D 系列 Vcpu)。 每當新的 VM 時要部署新的和現有的 Vcpu 的 VM 系列的使用方式的總和不得超過核准該特定的 VM 系列的 vCPU 配額。 此外，跨所有 VM 系列部署的總新的和現有的 vCPU 計數不應該超過核准訂用帳戶的總區域 Vcpu 配額。 如果超過這些配額，將不允許 VM 部署。
您可以從 Azure 入口網站要求增加 VM 系列的 Vcpu 配額限制。 VM 系列配額增加會自動增加總區域 Vcpu 限制，以相同數量。 

建立新的訂用帳戶時，可能不等於所有個別的 VM 系列的預設 vCPU 配額總和預設區域 Vcpu 總計。 這會導致訂用帳戶具有足夠的配額為每個個別的 VM 系列，您想要部署，但所有部署的總區域 vcpu 的配額不足。 在此情況下，您必須提交要求，以明確地增加總區域 Vcpu 限制。 區域 Vcpu 總計限制不能已核准的配額的總和超過跨區域的所有 VM 系列。

深入了解配額上[虛擬機器 vCPU 配額頁面](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)並[Azure 訂用帳戶和服務限制](https://aka.ms/quotalimits)頁面。 

