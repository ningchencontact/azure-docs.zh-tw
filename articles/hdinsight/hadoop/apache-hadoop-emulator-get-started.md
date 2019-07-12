---
title: 了解使用 Apache Hadoop 沙箱-模擬器-Azure HDInsight
description: '若要開始了解 Apache Hadoop 生態系統，您可以在 Azure 虛擬機器上從 Hortonworks 設定 Hadoop 沙箱。 '
keywords: hadoop 模擬器, hadoop 沙箱
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 2cb99cfe765e1d3444f362e591812f5088c78c0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393142"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>開始使用 Apache Hadoop 沙箱，虛擬機器上的模擬器

了解如何在虛擬機器上從 Hortonworks 安裝 Apache Hadoop 沙箱，以了解 Hadoop 生態系統。 沙箱提供本機開發環境，讓您了解 Hadoop、Hadoop 分散式檔案系統 (HDFS)，以及作業提交。 熟悉 Hadoop 之後，您就可以開始在 Azure 中使用 Hadoop 建立 HDInsight 叢集。 有關如何開始使用的詳細資訊，請參閱 [開始在 HDInsight 中使用 Hadoop](apache-hadoop-linux-tutorial-get-started.md)。

## <a name="prerequisites"></a>先決條件
* [Oracle VirtualBox](https://www.virtualbox.org/)。 請從[這裡](https://www.virtualbox.org/wiki/Downloads)下載並安裝。


## <a name="download-and-install-the-virtual-machine"></a>下載並安裝虛擬機器
1. 瀏覽至[Cloudera 下載](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)。

2. 按一下  **VIRTUALBOX**下方**選擇安裝類型**下載最新的 VM 上的 Hortonworks 沙箱。 登入，或完成的產品感興趣的表單。

1. 按一下按鈕**HDP 沙箱 （最新）** 來開始下載。

如需有關設定沙箱的指示，請參閱[沙箱部署與安裝指南](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)。

若要下載舊版 HDP 沙箱，請參閱下方的連結**舊版**。

## <a name="start-the-virtual-machine"></a>啟動虛擬機器

1. 開啟 [Oracle VM VirtualBox]。
2. 從 [檔案]  功能表中，按一下 [匯入設備]  ，然後指定「Hortonworks 沙箱」映像。
1. 選取「Hortonworks 沙箱」、按一下 [啟動]  ，然後選取 [正常啟動]  。 虛擬機器完成開機程序後會顯示登入指示。

    ![正常啟動](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. 開啟網頁瀏覽器並瀏覽至顯示的 URL (通常`http://127.0.0.1:8888`)。

## <a name="set-sandbox-passwords"></a>設定沙箱密碼

1. 從 [Hortonworks 沙箱] 頁面的 [入門]  步驟選取 [檢視進階選項]  。 在此頁面上使用該資訊，以透過 SSH 登入沙箱。 使用提供的名稱和密碼。

   > [!NOTE]
   > 如果您未安裝 SSH 用戶端，您可以使用虛擬機器在 **http://localhost:4200/** 所提供的網頁型 SSH。

    第一次使用 SSH 連線時，系統會提示您變更根帳戶的密碼。 輸入新密碼，這是您在使用 SSH 登入時使用的密碼。

2. 登入之後，輸入下列命令：

        ambari-admin-password-reset

    出現提示時，提供 Ambari 系統管理員帳戶的密碼。 當您存取 Ambari Web UI 時將會用到。

## <a name="use-hive-commands"></a>使用 Hive 命令

1. 在連往沙箱的 SSH 連線中，使用下列命令啟動 Hive 殼層：

        hive
2. 殼層啟動之後，使用下列命令檢視沙箱所提供的資料表︰

        show tables;
3. 使用下列命令從 `sample_07` 資料表擷取 10 個資料列︰

        select * from sample_07 limit 10;

## <a name="next-steps"></a>後續步驟
* [了解如何搭配使用 Visual Studio 與 Hortonworks 沙箱](../hdinsight-hadoop-emulator-visual-studio.md)
* [了解 Hortonworks 沙箱的訣竅](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop 教學課程 - 開始使用 HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

