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
ms.date: 12/04/2019
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875466"
---
**計算目標可以從一個定型作業重複使用到下一個**。 例如，將遠端 VM 連結至您的工作區之後，您可以將它重複用於多個作業。  針對機器學習管線，請針對每個計算目標使用適當的[管線步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

|訓練 &nbsp;目標|[自動化 ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML 管線](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning 設計工具](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[本機電腦](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 是 | &nbsp; | &nbsp; |
|[Azure Machine Learning 計算叢集](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 是 & <br/>超參數&nbsp;微調 | 是 | 是 |
|[遠端虛擬機器](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | 是 & <br/>超參數微調 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| 是 | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | 是 | &nbsp; |
