---
title: 存取以 Linux 為基礎之 HDInsight 上的 Apache Hadoop YARN 應用程式記錄 - Azure
description: 了解如何使用命令列和網頁瀏覽器存取以 Linux 為基礎之 HDInsight (Apache Hadoop) 叢集上的 YARN 應用程式記錄。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: c0c5ecfba97c61288d08681006645eab0bdd23f2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059470"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>在以 Linux 為基礎的 HDInsight 上存取 Apache Hadoop YARN 應用程式記錄

了解如何在 Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 叢集上存取 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) 應用程式記錄。

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) 提供已完成應用程式的相關泛型資訊

YARN Timeline Server 包含下列類型的資料：

* 應用程式識別碼，應用程式的唯一識別碼
* 啟動應用程式的使用者
* 為完成應用程式而進行之嘗試的相關資訊
* 任何指定之應用程式嘗試所使用的容器

## <a name="YARNAppsAndLogs"></a>YARN 應用程式和記錄

YARN 藉由將資源管理從應用程式排程/監視分離，支援多種程式設計模型 ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) 為其中之一)。 YARN 使用全域 *ResourceManager* (RM)、每一背景工作節點 *ResourceManager* (NM) 及每一應用程式 *ResourceManager* (AM)。 每一應用程式 AM 會與 RM 交涉用來執行您應用程式的資源 (CPU、記憶體、磁碟、網路)。 RM 會與 NM 合作來授與這些資源 (以「 *容器*」的形式授與)。 AM 則是負責追蹤 RM 指派給它之容器的進度。 視應用程式的本質而定，一個應用程式可能會需要許多容器。

每個應用程式都可能包含多個「應用程式嘗試」  。 如果應用程式失敗，系統可能會以新的嘗試來重試它。 每個嘗試都在容器中執行。 從某方面來說，容器提供由 YARN 應用程式所執行之作業的基本單位內容。 所有於容器內容之內所執行的作業，都會在容器所配置的單一背景工作節點上執行。 請參閱[Apache Hadoop YARN 概念](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)取得進一步的參考。

應用程式記錄 (和關聯的容器記錄) 在對有問題的 Hadoop 應用程式進行偵錯上相當重要。 YARN 能提供良好的架構，以收集、 彙總及儲存應用程式記錄[記錄檔彙總](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/)功能。 「記錄彙總」功能可使存取應用程式記錄更具確定性。 它能彙總背景工作節點上所有容器的記錄，並將它們依每個背景工作節點儲存成單一彙總記錄。 在應用程式完成之後，記錄檔會儲存在預設檔案系統上。 您的應用程式可能使用數百或數千個容器，但在單一背景工作節點上執行之所有容器的記錄一律彙總成單一檔案。 因此，應用程式使用的每一背景工作節點只會有 1 個記錄。 根據預設，HDInsight 叢集 3.0 版和更新版本上會啟用記錄彙總。 彙總記錄位於叢集的預設儲存體。 下列路徑是記錄的 HDFS 路徑︰

    /app-logs/<user>/logs/<applicationId>

在路徑中，`user` 是啟動應用程式的使用者名稱。 `applicationId` 是 YARN RM 指派給應用程式的唯一識別碼

您無法直接閱讀彙總的記錄，因為它們是以 [TFile][T-file]、由容器編製索引的 [二進位格式][binary-format] 撰寫。 對於您感興趣的應用程式或容器，請使用 YARN ResourceManager 記錄或 CLI 工具來檢視純文字記錄。

## <a name="yarn-cli-tools"></a>YARN CLI 工具

若要使用 YARN CLI 工具，您必須先使用 SSH 連接到 HDInsight 叢集。 如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

您可以執行下列其中一個命令，以檢視這些純文字記錄：

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

執行這些命令時，請指定 &lt;applicationId>、&lt;user-who-started-the-application>、&lt;containerId> 及 &lt;worker-node-address> 資訊。

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI 是在叢集前端節點上執行。 可透過 Ambari Web UI 存取。 請使用下列步驟來檢視 YARN 記錄：

1. 在您的網頁瀏覽器中，瀏覽至 https://CLUSTERNAME.azurehdinsight.net 。 將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。
2. 從左邊的服務清單中選取 [YARN]  。

    ![選取的 Yarn 服務](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. 在 [快速連結]  下拉式清單中，選取其中一個叢集前端節點，然後選取 [ResourceManager 記錄]  。

    ![Yarn 快速連結](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    您會看到一份 YARN 記錄的連結清單。

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
