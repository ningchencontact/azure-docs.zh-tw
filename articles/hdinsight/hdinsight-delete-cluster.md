---
title: 如何刪除 HDInsight 叢集 - Azure
description: 您可用來刪除 HDInsight 叢集的各種方式相關資訊。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 4df4fa29722dd3ad33cf1ce123877f04f9f4b4c1
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360383"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>使用您的瀏覽器、PowerShell 或 Azure 傳統 CLI 刪除 HDInsight 叢集

HDInsight 叢集的計費起自叢集建立時，終至叢集刪除時。 計費是以每分鐘按比例計算，因此不再使用時，請一律刪除您的叢集。 在此文件中，您將了解如何使用 [Azure 入口網站](https://portal.azure.com)、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/) 與 Azure 傳統 CLI 刪除叢集。

> [!IMPORTANT]  
> 刪除 HDInsight 叢集並不會刪除與叢集建立關聯的 Azure 儲存體帳戶或 Data Lake Storage。 您可以在日後重複使用這些服務中儲存的資料。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取您的 HDInsight 叢集。 如果您的 HDInsight 叢集並未釘選於儀表板上，您可以使用搜尋欄位依名稱搜尋它。
   
    ![入口網站搜尋](./media/hdinsight-delete-cluster/navbar.png)

2. 從叢集設定中，選取 [刪除] 圖示。 出現提示時，選取 [是] 以刪除叢集。
   
    ![刪除圖示](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在 PowerShell 提示中，使用下列命令來刪除叢集︰

    Remove-AzHDInsightCluster -ClusterName CLUSTERNAME

將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。

## <a name="azure-classic-cli"></a>Azure 傳統 CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

在提示中，使用下列命令來刪除叢集︰

    azure hdinsight cluster delete CLUSTERNAME

將 **CLUSTERNAME** 取代為 HDInsight 叢集的名稱。
