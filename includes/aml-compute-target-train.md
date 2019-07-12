---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/30/2019
ms.openlocfilehash: 1b62d4b2ac1bb69e2270c3202d29eb595df7aac4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806035"
---
**計算目標可以重複使用不同的訓練工作到下一個**。 例如，將遠端 VM 連結至您的工作區之後，您可以將它重複用於多個作業。

|訓練&nbsp;目標| GPU 支援 |[自動化的 ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML 管線](../articles/machine-learning/service/concept-ml-pipelines.md) | [視覺化介面](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[本機電腦](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 或許 | 是 | &nbsp; | &nbsp; |
|[Azure Machine Learning Compute](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 是 | 是 （& s) <br/>hyperparameter&nbsp;tuning | 是 | 是 |
|[遠端虛擬機器](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |是 | 是 （& s) <br/>超參數微調 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | 是 | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | 是 | &nbsp; |
