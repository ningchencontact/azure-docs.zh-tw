---
title: 將資料載入至 Azure 儲存體環境 - Team Data Science Process
description: 從 Azure Blob 儲存體來回移動資料
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 56c45bf6db79ded64574a44399712951e82c1c3e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472400"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>將資料載入至儲存體環境以便進行分析

Team Data Science Process 要求將資料內嵌或載入至各種不同的儲存體環境，以便在程序的每個階段中以最適當的方式處理或分析。 通常用於處理的資料目的地包括 Azure Blob 儲存體、SQL Azure 資料庫、Azure VM 上的 SQL Server、HDInsight (Hadoop) 和 Azure Machine Learning。 

下列文章說明如何將資料內嵌至可儲存和處理資料的各種目標環境中。

* 內嵌至/自 [Azure Blob 儲存體](move-azure-blob.md)
* 內嵌至 [Azure VM 上的 SQL Server](move-sql-server-virtual-machine.md)
* 內嵌至 [Azure SQL 資料庫](move-sql-azure.md)
* 內嵌至 [Hive 資料表](move-hive-tables.md)
* 內嵌至 [SQL 分割資料表](parallel-load-sql-partitioned-tables.md)
* 內嵌自[內部部署 SQL Server](move-sql-azure-adf.md)

技術和商務需求以及資料的初始位置、格式和大小，會決定需要擷取資料到其中的目標環境以達成您的分析目標。 以下案例並不常見：要求在數個環境之間移動資料來達成建構預測模型所需的各種工作。 比方說，這一系列工作可以包含資料瀏覽、前置處理、清除、向下取樣和模型定型。
