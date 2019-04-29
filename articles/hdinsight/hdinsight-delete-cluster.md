---
title: 如何刪除 HDInsight 叢集 - Azure
description: 您可用來刪除 HDInsight 叢集的各種方式相關資訊。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097167"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集

HDInsight 群集计费在创建群集之后便会开始，删除群集后才会停止。 計費是以每分鐘按比例計算，因此不再使用時，請一律刪除您的叢集。 在本文件中，您了解如何刪除叢集，使用[Azure 入口網站](https://portal.azure.com)， [Azure PowerShell Az 模組](https://docs.microsoft.com/powershell/azure/overview)，而[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

> [!IMPORTANT]  
> 刪除 HDInsight 叢集並不會刪除與叢集建立關聯的 Azure 儲存體帳戶或 Data Lake Storage。 您可以在日後重複使用這些服務中儲存的資料。

## <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 從左側功能表中，瀏覽至**所有的服務** > **Analytics** > **HDInsight 叢集**，然後選取您的叢集。

3. 從 [預設] 檢視中，選取**刪除**圖示。 依照提示來刪除您的叢集。
   
    ![刪除圖示](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell Az module

取代`CLUSTERNAME`下列程式碼中的 HDInsight 叢集的名稱。 從 PowerShell 提示字元中，輸入下列命令來刪除叢集：

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

取代`CLUSTERNAME`HDInsight 叢集的名稱和`RESOURCEGROUP`的下列程式碼中的資源群組名稱。  在命令提示字元中，輸入下列命令來刪除叢集：

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
