---
title: 調整 Azure 資料總管叢集的大小，適應變更的需求
description: 本文描述根據變更需求相應增加及相應減少 Azure 資料總管叢集的步驟。
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415329"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>管理叢集相應增加以因應不斷變化的需求

適當調整叢集的大小對 Azure 資料總管的效能來說非常重要。 但是叢集上的需求無法 100% 準確預測。 靜態叢集大小可能導致使用量過低或使用量過高，兩者皆不理想。 更好的方法是*調整*叢集，根據變更的需求新增或移除容量和 CPU。 調整有兩個工作流程：相應增加和相應放大。本文說明如何管理叢集相應增加。

1. 瀏覽至您的叢集，然後在 [設定] 下方選取 [相應增加]。

    您將能看見可用 SKU 的清單。 例如，下圖中只有一個可用 SKU：D14_V2。

    ![相應增加](media/manage-cluster-scale-up/scale-up.png)

    D13_v2 已停用，因為它是叢集目前的 SKU。 L8 和 L16 也已停用，因為它們不適用於叢集所在的區域。

1. 若要變更 SKU，請選取您所需的 SKU，然後按 [選取] 按鈕。

> [!NOTE]
> 相應增加程序可能需要幾分鐘的時間，而在此期間您的叢集將會暫止。 請注意，相應減少可能會導致叢集效能下降。

您現在已針對您的 Azure 資料總管叢集執行相應增加或相應減少作業。 您已可以執行[叢集相應放大](manage-cluster-scale-out.md) (也稱為自動調整)，來根據您指定的計量以動態方式進行調整。

若您需要叢集調整問題的協助，請在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中開立支援要求。
