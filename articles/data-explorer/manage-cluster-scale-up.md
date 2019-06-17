---
title: 相應增加 Azure 資料總管群集，來滿足多變的需求
description: 本文說明相應增加和相應減少根據變更的隨選 Azure 資料總管叢集的步驟。
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 1f130f79b6b6924559e1693e1eef8ced2972b3d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60758685"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>管理叢集相應增加以因應不斷變化的需求

有兩個工作流程在調整 Azure 資料總管叢集： 向上延展及[向外延展](manage-cluster-scale-out.md)。本文說明如何管理叢集相應增加。

適當調整叢集的大小對 Azure 資料總管的效能來說非常重要。 但是，在叢集上的需求無法預測以絕對的精確度。 靜態的叢集大小可能會導致使用量過低或使用量過高，兩者都不適合。 更好的方法是以*擴展*叢集中，新增及移除容量和與變更要求的 CPU 資源。 

## <a name="steps-to-scale-up"></a>若要相應增加的步驟
1. 請移至您的叢集。 底下**設定**，選取**相應增加**。

    您會看到一份可用的 Sku。 例如，在下圖中，只有四個 Sku 可。

    ![相應增加](media/manage-cluster-scale-up/scale-up.png)

    Sku 會停用，因為它們目前的 SKU，或它們都無法使用叢集所在的區域中。

1. 若要變更您的 SKU，選取您想要並選擇的 SKU**選取** 按鈕。

> [!NOTE]
> 相應增加程序可能需要幾分鐘的時間，並在這段期間即會暫止您的叢集。 請注意，相應減少可能會導致叢集效能下降。

您現在已完成您的 Azure 資料總管叢集的相應增加或相應減少作業。

## <a name="next-steps"></a>後續步驟
您也可以[管理叢集向外延展](manage-cluster-scale-out.md)動態相應放大的執行個體計數會根據您指定的計量。

如果您需要叢集調整問題，協助[開啟支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)在 Azure 入口網站中。

遵循這篇文章，以監視您的資源使用量：[監視 Azure 資料總管效能、 健全狀況和使用量計量](using-metrics.md)。
