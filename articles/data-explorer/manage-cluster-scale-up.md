---
title: 調整 Azure 資料總管叢集的大小，適應變更的需求
description: 本文描述根據變更需求相應放大及相應縮小 Azure 資料總管叢集的步驟。
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735311"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>管理叢集相應增加以因應不斷變化的需求

適當調整叢集的大小對 Azure 資料總管的效能來說非常重要。 但是叢集上的需求無法 100% 準確預測。 靜態叢集大小可能導致使用量過低或使用量過高，兩者皆不理想。 更好的方法是*調整*叢集，根據變更的需求新增或移除容量和 CPU。 本文說明如何管理叢集相應增加。

瀏覽至您的叢集，然後在 [設定] 下方選取 [相應增加]。

接著，系統會提供可用的 SKU 清單。 您可以從已啟用的卡清單中選擇。 就下圖中的範例而言，可從 D14_vs 中選擇的 SKU 只有一個。

![相應增加](media/manage-cluster-scale-up/scale-up.png)

D13_v2 已停用，因為這是叢集目前的 SKU。 L8 和 L16 也已停用，因為它們不適用於叢集所在的區域。

若要變更 SKU，請直接按一下您要使用的 SKU，然後按一下 [選取] 按鈕。

[!NOTE] 相應增加程序可能需要幾分鐘的時間，而在此期間您的叢集將會暫止。 請注意，相應減少可能會導致叢集效能下降。

若您需要叢集調整問題的協助，請在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)中開立支援要求。