---
title: Azure Batch AI 的現況為何？ | Microsoft Docs
description: 了解 Azure Batch AI 的現況及 Azure Machine Learning 服務計算選項。
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436869"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI 的現況為何？

**Azure Batch AI 服務將在 3 月淘汰。** Batch AI 的大規模定型與計分功能目前已在 [Azure Machine Learning 服務](../machine-learning/service/overview-what-is-azure-ml.md)中提供，其已於 2018 年 12 月 4 日正式推出。

除了許多其他機器學習功能，Azure Machine Learning 服務還包括雲端式受控計算目標來對機器學習服務模型進行定型、部署和評分。 此計算目標稱為 [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。 [立即開始移轉並使用它](#migrate)。 您可以透過其 [Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md)、命令列介面及 [Azure 入口網站](../machine-learning/service/quickstart-get-started.md)來與 Azure Machine Learning 服務進行互動。

## <a name="support-timeline"></a>支援時間表

| 日期 | Batch AI 服務支援詳細資料 |
| ---- |-----------------|
| 2018 年&nbsp;12 月&nbsp;14 日| 和以前一樣，繼續使用現有的 Azure Batch AI 訂用帳戶。 不過，註冊**新的訂用帳戶**已不再可行，而且將不會對此服務進行任何新投資。|
| 2019 年&nbsp;3 月&nbsp;31 日 | 在這個日期之後，現有的 Batch AI 訂用帳戶將無法運作。 |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>我應如何移轉？

若要避免中斷您的應用程式並受益於最新功能，請在 2019 年 3 月 31 日之前採取下列步驟：

1. 建立 Azure Machine Learning 服務工作區並開始使用：
    + [以 Python 為基礎的快速入門](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [以 Azure 入口網站為基礎的快速入門](../machine-learning/service/quickstart-get-started.md)

1. 設定用來為模型定型的 [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)。

1. 更新您的指令碼以使用 Azure Machine Learning Compute。

## <a name="support"></a>支援

支援適用於想要移轉至更詳盡之 [Azure Machine Learning 服務](https://aka.ms/aml-docs)的現有客戶。

如果 Azure Machine Learning 服務不符合您對於所支援功能存在於 Batch AI 服務中的需求，請向支援小組開啟 Batch AI 支援要求，以將您的訂用帳戶列入允許清單來使用 Batch AI，直到服務淘汰為止。

## <a name="next-steps"></a>後續步驟

+ 請參閱 [Azure Machine Learning 服務概觀](../machine-learning/service/overview-what-is-azure-ml.md)。

+ 使用 Azure Machine Learning 服務[為模型定型設定計算目標](../machine-learning/service/how-to-set-up-training-targets.md)。

+ 檢閱 [Azure 藍圖](https://azure.microsoft.com/updates/)來了解其他 Azure 服務更新。
