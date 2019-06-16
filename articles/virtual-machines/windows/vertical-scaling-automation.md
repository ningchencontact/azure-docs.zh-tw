---
title: 使用 Azure 自動化來垂直調整 Azure 虛擬機器 | Microsoft Docs
description: 使用「Azure 自動化」來垂直調整「Windows 虛擬機器」以回應監視警示
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f46164d52d22b54c0c4f4d707e76be274ebaaf4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080153"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>使用 Azure 自動化來垂直調整 Windows VM

垂直調整大小是指為回應工作負載而增加或減少電腦資源的程序。 在 Azure 中，可以透過變更虛擬機器的大小來完成。 在下列情況中這種方式很有幫助

* 如果不常使用虛擬機器，可以將其調整成較小的規模，以降低每月成本
* 如果虛擬機器預期會有尖峰負載，可以將其調整成較大的規模，以增加其容量

完成此作業的步驟大致如下

1. 將 Azure 自動化設定為可存取您的虛擬機器
2. 將 Azure 自動化垂直調整大小 Runbook 匯入訂用帳戶
3. 將 Webhook 加入您的 Runbook 中
4. 將警示加入虛擬機器中

> [!NOTE]
> 因為這是第一部虛擬機器大小的緣故，所以它可以調整的大小，會受限於目前虛擬機器部署所在之叢集中是否可使用其他大小。 本文所用的已發佈自動化 Runbook 中，已考量了這個情況，只會於下列成對的 VM 大小內調整大小。 這表示 Standard_D1v2 虛擬機器不會突然相應增加為 Standard_G5 或相應減少為 Basic_A0。 也不支援限制的虛擬機器大小相應增加/減少。 您可以在以下大小配對之間選擇調整︰
> 
> | 成對的調整 VM 大小 |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | 標準_D1 |標準_D4 |
> | 標準_D11 |標準_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>將 Azure 自動化設定為可存取您的虛擬機器
您首先要做的是建立 Azure 自動化帳戶，其要裝載用來調整虛擬機器的 Runbook。 最近，自動化服務引進「執行身分帳戶」功能，極輕鬆即可代表使用者設定服務主體來自動執行 Runbook。 您可以在下文中閱讀更多相關資訊：

* [使用 Azure 執行身分帳戶驗證 Runbook](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>將 Azure 自動化垂直調整大小 Runbook 匯入訂用帳戶
Azure 自動化 Runbook 資源庫中已發佈的垂直調整虛擬機器大小所需之 Runbook。 您必須將其匯入您的訂用帳戶。 您可以閱讀下列文章，了解如何匯入 Runbook。

* [Azure 自動化的 Runbook 和模組資源庫](../../automation/automation-runbook-gallery.md)

需要如下圖所示匯入 Runbook

![匯入 Runbook](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>將 Webhook 加入您的 Runbook 中
匯入 Runbook 之後，需要將 Webhook 加入 Runbook 中，如此即可從虛擬機器發出的警示加以觸發。 如需為 Runbook 建立 Webhook 的詳細資訊，請參閱

* [Azure 自動化 Webhook](../../automation/automation-webhooks.md)

關閉 Webhook 對話方塊之前，請務必複製 Webhook，因為在下一節中將需要此 Webhook。

## <a name="add-an-alert-to-your-virtual-machine"></a>將警示加入虛擬機器中
1. 選取虛擬機器設定
2. 選取 [警示規則]
3. 選取 [加入警示]
4. 選取要引發警示的衡量標準
5. 選取要符合才會引發警示的條件
6. 針對步驟 5 的條件選取臨界值。 要符合的
7. 選取監視服務將檢查步驟 5 和 6 之條件和臨界值的期間
8. 貼上從上一節複製的 Webhook。

![將警示加入虛擬機器 1 中](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![將警示加入虛擬機器 2 中](./media/vertical-scaling-automation/add-alert-webhook-2.png)

