---
title: 管理 Azure HDInsight 叢集使用 Azure CLI
description: 了解如何使用 Azure CLI 來管理 Azure HDInsight 叢集。 叢集類型包括 Apache Hadoop、 Spark、 HBase、 Storm、 Kafka、 互動式查詢和 ML 服務。
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 7c12831c43762ddc776e8d5701f002be97992cbc
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859971"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>管理 Azure HDInsight 叢集使用 Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

了解如何使用[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)來管理 Azure HDInsight 叢集。 Azure 命令列介面 (CLI) 是用來管理 Azure 資源的 Microsoft 跨平台命令列體驗。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

* Azure CLI。 如果您尚未安裝 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)的步驟。

* 在 HDInsight 上 Apache Hadoop 叢集。 請參閱[開始使用 Linux 上的 HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

## <a name="connect-to-azure"></a>連線至 Azure

登入您的 Azure 訂用帳戶。 如果您打算使用 Azure Cloud Shell，只要選取**試試**的程式碼區塊右上角。 否則，請輸入下列命令：

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>列出叢集

使用[az hdinsight 清單](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list)來列出叢集。 編輯下面命令取代`RESOURCE_GROUP_NAME`與您的資源群組名稱，然後輸入命令：

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>顯示叢集

使用[az hdinsight 顯示](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show)以顯示指定的叢集資訊。 編輯下列命令，來取代`RESOURCE_GROUP_NAME`，和`CLUSTER_NAME`的相關資訊，然後輸入命令：

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>刪除叢集

使用[az hdinsight 刪除](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete)刪除指定的叢集。 編輯下列命令，來取代`RESOURCE_GROUP_NAME`，和`CLUSTER_NAME`的相關資訊，然後輸入命令：

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

您也可以刪除包含叢集的資源群組來刪除叢集。 請注意，這會刪除群組包括預設儲存體帳戶中的所有資源。

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>調整叢集

使用[調整大小，az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize)調整成指定的大小指定的 HDInsight 叢集的大小。 編輯下列命令，來取代`RESOURCE_GROUP_NAME`，和`CLUSTER_NAME`的相關資訊。 取代`TARGET_INSTANCE_COUNT`適用於您叢集的背景工作角色節點所需的數目。 如需有關如何調整叢集的詳細資訊，請參閱 <<c0> [ 調整 HDInsight 叢集](./hdinsight-scaling-best-practices.md)。 輸入命令：

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>後續步驟

本文中，您學到如何執行不同的 HDInsight 叢集管理工作。 若要深入了解，請參閱下列文章：

* [使用 Azure 入口網站管理 HDInsight 中的 Apache Hadoop 叢集](hdinsight-administer-use-portal-linux.md)
* [使用 Azure PowerShell 管理 HDInsight](hdinsight-administer-use-powershell.md)
* [開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [開始使用 Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
