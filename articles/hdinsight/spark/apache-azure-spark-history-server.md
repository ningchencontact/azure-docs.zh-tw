---
title: 使用擴充的 Spark 記錄伺服器針對 Spark 應用程式進行偵錯和診斷 - Azure HDInsight
description: 使用擴充的 Spark 記錄伺服器對 Spark 應用程式進行偵錯和診斷 - Azure HDInsight。
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2018
ms.openlocfilehash: b514f23f2e8a43f99fd5bf5c3afb5ed625ad4472
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046570"
---
# <a name="use-extended-spark-history-server-to-debug-and-diagnose-spark-applications"></a>使用擴充的 Spark 記錄伺服器對 Spark 應用程式進行偵錯和診斷

本文將說明如何使用擴充的 Spark 記錄伺服器，對已完成和執行中的 Spark 應用程式進行偵錯及診斷。 此擴充版本目前包含 [資料] 索引標籤和 [圖表] 索引標籤。在 [資料] 索引標籤中，使用者可以檢查 Spark 作業的輸入和輸出。 在 [圖表] 索引標籤中，使用者可以檢查資料流程，並重新執行作業圖表。

## <a name="open-the-spark-history-server"></a>開啟 Spark 歷程記錄伺服器

「Spark 記錄伺服器」是已完成和執行中 Spark 應用程式的 Web UI。 

### <a name="to-open-the-spark-history-server-web-ui-from-azure-portal"></a>從 Azure 入口網站開啟 Spark 記錄伺服器 Web UI

1. 從 [Azure 入口網站](https://portal.azure.com/)，開啟 Spark 叢集。 如需詳細資訊，請參閱[列出和顯示叢集](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters)。
2. 從 [快速連結]，按一下 [叢集儀表板]，然後按一下 [Spark 記錄伺服器]。 出現提示時，輸入 Spark 叢集的系統管理員認證。 

    ![Spark 歷程記錄伺服器](./media/apache-azure-spark-history-server/launch-history-server.png "Spark 歷程記錄伺服器")

### <a name="to-open-the-spark-history-server-web-ui-by-url"></a>透過 URL 開啟 Spark 記錄伺服器 Web UI
藉由瀏覽至下列 URL 來開啟 Spark 記錄伺服器，並以客戶的 Spark 叢集名稱取代 <ClusterName>。

   ```
   https://<ClusterName>.azurehdinsight.net/sparkhistory
   ```

「Spark 記錄伺服器」Web UI 看起來像這樣：

![HDInsight Spark 記錄伺服器](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)


## <a name="open-the-data-tab-from-spark-history-server"></a>從 Spark 記錄伺服器開啟 [資料] 索引標籤
選取作業識別碼，然後按一下工具功能表上的 [資料]，以取得資料檢視。

+ 您可以分別選取 [輸入]、[輸出]和 [資料表作業] 索引標籤來檢查這些項目。

    ![[資料] 索引標籤](./media/apache-azure-spark-history-server/sparkui-data-tabs.png)

+ 按一下 [複製] 按鈕可複製所有資料列。

    ![資料複製](./media/apache-azure-spark-history-server/sparkui-data-copy.png)

+ 按一下 [csv] 按鈕，可將所有資料儲存為 CSV 檔案。

    ![資料儲存](./media/apache-azure-spark-history-server/sparkui-data-save.png)

+ 在 [搜尋] 欄位中輸入關鍵字，即可進行搜尋，而搜尋結果會立即顯示。

    ![資料搜尋](./media/apache-azure-spark-history-server/sparkui-data-search.png)

+ 按一下資料行標頭可排序資料表，按一下加號可展開資料列，以顯示更多詳細資料，或按一下減號來摺疊資料列。

    ![資料表](./media/apache-azure-spark-history-server/sparkui-data-table.png)

+ 若要下載單一檔案，請按一下右側的 [部分下載] 按鈕，然後選取要下載到本機的檔案，如果檔案已不存在，顯示錯誤訊息的新索引標籤會隨即開啟。

    ![資料的下載資料列](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 若要複製完整路徑或相對路徑，可從展開的下載功能表中選取 [複製完整路徑] 或 [複製相對路徑]。 針對 Azure Data Lake Storage 檔案，[在 Azure 儲存體總管中開啟] 會啟動 Azure 儲存體總管，並在登入時移至該資料夾。

    ![資料的複製路徑](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 按一下資料表下方的編號，可在單一頁面上顯示太多資料列時，瀏覽多個頁面。 

    ![資料頁面](./media/apache-azure-spark-history-server/sparkui-data-page.png)

+ 將滑鼠停留在 [資料] 旁的問號上方可顯示工具提示，或按一下問號以取得詳細資訊。

    ![資料的詳細資訊](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ 按一下 [提供意見反應給我們]，可將意見反應和問題傳送給我們。

    ![圖表的意見反應](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="open-the-graph-tab-from-spark-history-server"></a>從 Spark 記錄伺服器開啟 [圖表] 索引標籤
選取作業識別碼，然後按一下工具功能表上的 [圖表]，以取得作業圖表檢視。

+ 透過產生的作業圖表來檢查您的作業概觀。 

+ 根據預設，作業圖表會顯示所有作業，並且可依據**作業識別碼**進行篩選。

    ![圖表作業識別碼](./media/apache-azure-spark-history-server/sparkui-graph-jobid.png)

+ 系統會預設為選取 [進度]，使用者可以在 [顯示] 的下拉式清單中選取 [讀取]/[寫入] 來檢查資料流程。

    ![圖表顯示](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    圖表節點會以表示熱度圖的色彩來顯示。

    ![圖表熱度圖](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ 按一下 [播放] 按鈕可播放作業，而按一下 [停止] 按鈕可隨時停止。 播放時，工作會以不同色彩來顯示，以表示不同狀態：

    + 綠色表示成功：作業已順利完成。
    + 橘色表示重試：失敗但不會影響作業最終結果的工作執行個體。 這些工作有之後可能會成功的重複或重試執行個體。
    + 紅色表示失敗：工作失敗。
    + 藍色表示執行中：工作正在執行中。
    + 白色表示已略過或等候中：工作在等候執行，或已略過該階段。

    ![正在執行的圖表色彩範例](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)
 
    ![失敗的圖表色彩範例](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)
 
    > [!NOTE]
    > 每個作業都可播放。 如果作業沒有任何階段或尚未完成，則不支援播放。


+ 捲動滑鼠滾輪可縮放作業圖表，或按一下 [縮放至適當比例]，以調整成符合螢幕的大小。
 
    ![圖表縮放至適當比例](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 如果有失敗的工作，將滑鼠停留在圖表節點上方可查看工具提示，然後按一下階段可開啟階段頁面。

    ![圖表工具提示](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 作業圖表節點會顯示每個階段的下列資訊：
    + 識別碼。
    + 名稱或描述。
    + 工作總數。
    + 讀取的資料：輸入大小和隨機讀取大小的總和。
    + 寫入的資料：輸出大小和隨機寫入大小的總和。
    + 執行時間：第一次嘗試開始時和最後一次嘗試完成時之間的時間。
    + 資料列計數：輸入記錄、輸出記錄、隨機讀取記錄和隨機寫入記錄的總和。
    + 進度。

    > [!NOTE]
    > 根據預設，作業圖表節點會顯示每個階段 (不包括階段執行時間) 最後一次嘗試時的資訊 ，但是播放期間的圖表節點會顯示每一次嘗試的資訊。

    > [!NOTE]
    > 針對讀取和寫入的資料大小，我們使用 1MB = 1000 KB = 1000 * 1000 個位元組。

+ 按一下 [提供意見反應給我們]，可將意見反應和問題傳送給我們。

    ![圖表的意見反應](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)


## <a name="faq"></a>常見問題集

### <a name="1-revert-to-community-version"></a>1.還原為社群版本

若要還原為社群版本，請執行下列步驟：

1. 在 Ambari 中開啟叢集。 按一下左側面板中的 [Spark2]。
2. 按一下 [設定] 索引標籤。
3. 展開 **Custom spark2-defaults** 群組。
4. 按一下 [新增屬性]，新增 **spark.ui.enhancement.enabled=false**，然後儲存。
5. 屬性現在會設定為 **false**。
6. 按一下 **[儲存]** 儲存組態。

    ![功能關閉](./media/apache-azure-spark-history-server/sparkui-turn-off.png)

7. 按一下左側面板中的 [Spark2]，在 [摘要] 索引標籤下方，按一下 [Spark2 記錄伺服器]。

    ![重新啟動伺服器 1](./media/apache-azure-spark-history-server/sparkui-restart-1.png) 

8. 按一下 **Spark2 記錄伺服器**的 [重新啟動]，以重新啟動記錄伺服器。

    ![重新啟動伺服器 2](./media/apache-azure-spark-history-server/sparkui-restart-2.png)  

9. Spark 記錄伺服器 Web UI 在重新整理後就會還原成社群版本。

### <a name="2-upload-history-server-event"></a>2.上傳記錄伺服器事件

如果您遇到記錄伺服器錯誤，請依照下列步驟來提供事件：
1. 按一下記錄伺服器 Web UI 中的 [下載] 來下載事件。

    ![下載事件](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 從 [資料]/[圖表] 索引標籤中按一下 [提供意見反應給我們]。

    ![圖表的意見反應](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供錯誤的標題和描述，並將 zip 檔拖曳至 [編輯] 欄位中，然後按一下 [提交新問題]。

    ![檔案問題](./media/apache-azure-spark-history-server/sparkui-file-issue.png)


### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3.針對 hotfix 案例升級 jar 檔案

如果您想要使用 hotfix 來進行升級，請使用下列指令碼，這將會升級 spark-enhancement.jar*。

**upgrade_spark_enhancement.sh**：

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

**使用量**： 

`upgrade_spark_enhancement.sh https://${jar_path}`

**範例**：

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.tgz` 

**從 Azure 入口網站中使用 Bash 檔案**

1. 啟動 [Azure 入口網站](https://ms.portal.azure.com)，然後選取您的叢集。
2. 按一下 [指令碼動作]，然後按一下 [提交新項目]。 完成 [提交指令碼動作] 表單，然後按一下 [建立] 按鈕。
    
    + **指令碼類型**：選取 [自訂]。
    + **名稱**：指定指令碼名稱。
    + **Bash 指令碼 URI**：將 Bash 檔案上傳到私人叢集，然後複製此處的 URL。 或者，使用提供的 URI。
    
   ```upgrade_spark_enhancement
    https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh
   ```

    + 核取 [標頭] 和 [背景工作角色]。
    + **參數**：設定 Bash 使用量後方的參數。

    ![上傳記錄或升級 hotfix](./media/apache-azure-spark-history-server/sparkui-upload2.png)


## <a name="known-issue"></a>已知問題

1.  目前，此功能只適用於 Spark 2.3 叢集。

2.  使用 RDD 的輸入/輸出資料不會顯示在 [資料] 索引標籤中。

## <a name="next-steps"></a>後續步驟

* [在 HDInsight 上管理 Spark 叢集的資源](apache-spark-resource-manager.md)
* [設定 Spark 設定](apache-spark-settings.md)


## <a name="contact-us"></a>與我們連絡

如果您有任何意見反應，或在使用此工具時遇到任何其他問題，請傳送電子郵件到 ([hdivstool@microsoft.com](mailto:hdivstool@microsoft.com))。
