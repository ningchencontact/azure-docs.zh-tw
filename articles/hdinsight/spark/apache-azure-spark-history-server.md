---
title: 擴充 Spark 歷程記錄伺服器以進行偵錯工具-Azure HDInsight
description: 使用擴充的 Spark 記錄伺服器對 Spark 應用程式進行偵錯和診斷 - Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 7e9ab0e41086a4c9478f95c5a56754640feeab4e
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561810"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>使用擴充的 Apache Spark 記錄伺服器對 Apache Spark 應用程式進行偵錯和診斷

本文將說明如何使用擴充的 Apache Spark 記錄伺服器，對已完成和執行中的 Spark 應用程式進行偵錯及診斷。 延伸模組包含 [資料] 索引標籤和 [圖表] 索引標籤和 [診斷]在 [**資料**] 索引標籤上，使用者可以檢查 Spark 作業的輸入和輸出資料。 在 [圖表] 索引標籤上，使用者可以檢查資料流程，並重新執行作業圖表。 在 [**診斷**] 索引標籤上，使用者可以參考**資料扭曲**、**時間誤差**和**執行程式使用量分析**。

## <a name="get-access-to-apache-spark-history-server"></a>存取 Apache Spark 歷程記錄伺服器

「Apache Spark 記錄伺服器」是已完成和執行中 Spark 應用程式的 Web UI。

### <a name="open-the-apache-spark-history-server-web-ui-from-azure-portal"></a>從 Azure 入口網站開啟 Apache Spark 歷程記錄伺服器 Web UI

1. 從 [Azure 入口網站](https://portal.azure.com/)，開啟 Spark 叢集。 如需詳細資訊，請參閱[列出和顯示叢集](../hdinsight-administer-use-portal-linux.md#showClusters)。
2. 從叢集**儀表板**中，選取 [ **Spark 歷程記錄伺服器**]。 出現提示時，輸入 Spark 叢集的系統管理員認證。

    ![入口網站啟動 Spark 歷程記錄伺服器](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Spark 歷程記錄伺服器")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>透過 URL 開啟 Spark 記錄伺服器 Web UI

流覽至 `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` （其中 CLUSTERNAME 是您的 Spark 叢集名稱），以開啟 Spark 歷程記錄伺服器。

Spark 歷程記錄伺服器 web UI 看起來可能像這樣：

![HDInsight Spark 記錄伺服器](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="data-tab-in-spark-history-server"></a>Spark 歷程記錄伺服器中的 [資料] 索引標籤

選取 [作業識別碼]，然後選取 [工具] 功能表上的 [**資料**] 來取得資料檢視。

+ 分別選取索引標籤，以檢查**輸入**、**輸出**和**資料表作業**。

    ![Spark 應用程式索引標籤的資料](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ 選取 [**複製**] 按鈕來複製所有資料列。

    ![Spark 應用程式複製的資料](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ 選取 [ **csv**] 按鈕，將所有資料儲存為 csv 檔案。

    ![Spark 應用程式儲存的資料](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ 在 [搜尋] 欄位中輸入關鍵字，即可進行搜尋，而搜尋結果會立即顯示。

    ![Spark 應用程式搜尋的資料](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ 選取要排序資料表的資料行標頭，選取加號展開資料列以顯示更多詳細資料，或選取減號來折迭列。

    ![Spark 應用程式資料表的資料](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ 若要下載單一檔案，請選取位於右側的 [**部分下載**] 按鈕，然後將選取的檔案下載到本機，如果檔案已不存在，就會開啟新的索引標籤來顯示錯誤訊息。

    ![Spark 應用程式下載的資料列](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ 若要複製完整路徑或相對路徑，可從展開的下載功能表中選取 [複製完整路徑] 或 [複製相對路徑]。 針對 azure data lake storage 檔案，**在中開啟 Azure 儲存體總管**會 Azure 儲存體總管啟動，並在登入時尋找資料夾。

    ![Spark 應用程式複製路徑的資料](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ 選取資料表下方的數位，以在一頁中顯示太多資料列時，流覽頁面。

    ![Spark 應用程式的資料頁面](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ 將滑鼠停留在 [資料] 旁的問號，以顯示工具提示，或選取問號以取得詳細資訊。

    ![Spark 應用程式的資料詳細資訊](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+ 按一下 [提供意見反應給我們]，可將意見反應和問題傳送給我們。

    ![Spark graph 再次提供意見反應給我們](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Apache Spark 歷程記錄伺服器中的 [圖表] 索引標籤

選取作業識別碼，然後按一下工具功能表上的 [圖表]，以取得作業圖表檢視。

+ 查看所產生之作業圖形的作業總覽。

+ 根據預設，作業圖表會顯示所有作業，並且可依據**作業識別碼**進行篩選。

    ![Spark 應用程式和作業圖形作業識別碼](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ 系統會預設為選取 [進度]，使用者可以在 [顯示] 的下拉式清單中選取 [讀取]/[寫入] 來檢查資料流程。

    ![Spark 應用程式和作業圖形顯示](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

    圖表節點會以表示熱度圖的色彩來顯示。

    ![Spark 應用程式和作業圖表熱度圖](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)

+ 選取 [**播放**] 按鈕來播放作業，並選取 [停止] 按鈕隨時停止。 播放時，工作會以不同色彩來顯示，以表示不同狀態：

    |色彩 |描述 |
    |---|---|
    |綠色|作業已成功完成。|
    |Orange|失敗但不會影響作業最終結果的工作實例。 這些工作有之後可能會成功的重複或重試執行個體。|
    |藍色|工作正在執行中。|
    |白色|工作正在等候執行，或已略過該階段。|
    |紅色|工作失敗。|

    ![Spark 應用程式和作業圖形色彩範例，執行中](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

    已略過的階段會以白色顯示。
    ![Spark 應用程式和作業圖形色彩範例，略過](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Spark 應用程式和作業圖形色彩範例，失敗](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

    > [!NOTE]  
    > 每個作業都可播放。 針對不完整的作業，不支援播放。

+ 捲動滑鼠滾輪可縮放作業圖表，或按一下 [縮放至適當比例]，以調整成符合螢幕的大小。

    ![Spark 應用程式和作業圖形縮放至適當比例](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ 如果有失敗的工作，將滑鼠停留在圖表節點上方可查看工具提示，然後按一下階段可開啟階段頁面。

    ![Spark 應用程式和作業圖表工具提示](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ 在 [作業圖表] 索引標籤中，若階段有符合以下條件的工作，將會顯示工具提示與小圖示：
  + 資料扭曲：資料讀取大小 > 此階段中所有工作的平均資料讀取大小 * 2，且資料讀取大小 > 10 MB。
  + 時間扭曲：執行時間 > 此階段所含所有工作的平均執行時間 * 2，且執行時間 > 2 分鐘。

    ![Spark 應用程式和作業圖形扭曲圖示](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

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

+ 選取 [**提供意見**反應] 以傳送問題的意見反應。

    ![Spark 應用程式和作業圖形意見反應](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="diagnosis-tab-in-apache-spark-history-server"></a>Apache Spark 歷程記錄伺服器中的 [診斷] 索引標籤

選取 [作業識別碼]，然後選取 [工具] 功能表上的 [**診斷**]，以取得作業診斷視圖。 [診斷] 所有標籤包括 [資料扭曲]、[時間扭曲] 與 [執行程式使用狀況分析]。

+ 分別選取索引標籤，以查看**資料扭曲**、**時間誤差**和**執行程式使用量分析**。

    ![再次 SparkUI 診斷資料扭曲索引標籤](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>資料扭曲

選取 [**資料扭曲**] 索引標籤，就會根據指定的參數顯示對應的扭曲工作。

+ **指定參數**-第一個區段會顯示用來偵測資料扭曲的參數。 內建規則是：工作資料讀取大於平均工作資料讀取的3倍，而工作資料讀取超過 10 MB。 若要為扭曲工作定義您的自己的規則，您可以選擇您的參數，[扭曲階段] 與 [扭曲字元] 區段將相應重新整理。

+ **扭曲階段**-第二個區段會顯示階段，其中有扭曲的工作符合上面指定的準則。 如果階段中有多個扭曲工作，扭曲階段資料表只會顯示最扭曲的工作（例如，資料扭曲的最大資料）。

    ![sparkui 診斷資料扭曲索引標籤](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

+ **扭曲圖表** – 當扭曲階段表格中的列已被選取時，扭曲表格會根據資料讀取與執行時間來顯示更多工作分派詳細資料。 扭曲工作會標示為紅色，且正常工作會標示為藍色。 針對效能考量，圖表只會顯示最多 100 個範例工作。 工作詳細資料會顯示在右下角的窗格中。

    ![階段10的 sparkui 扭曲圖](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>時間扭曲

[時間扭曲] 索引標籤會根據工作執行時間來顯示扭曲工作。

+ **指定參數**-第一個區段會顯示用來偵測時間誤差的參數。 偵測時間誤差的預設準則是：工作執行時間大於平均執行時間的三倍，而工作執行時間大於30秒。 您可以根據您的需求來變更參數。 [扭曲階段] 與 [扭曲圖表] 會顯示對應的階段與工作資序，就像上面的 [資料扭曲] 索引標籤一樣。

+ 選取 [**時間誤差**]，然後根據 [**指定參數**] 區段中所設定的參數，在 [**扭曲階段**] 區段中顯示篩選的結果。 在 [**扭曲階段**] 區段中選取一個專案，然後對應的圖表會在 [section3] 中繪製，而且工作詳細資料會顯示在右下方面板中。

    ![sparkui 診斷時間誤差區段](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>執行程式使用狀況分析

執行程式使用狀況圖表會將 Spark 作業實際執行程式配置與執行狀態視覺化。  

+ 選取 **[執行程式使用狀況分析**]，然後繪製有關執行程式使用方式的四個類型曲線，包括已配置的執行**程式、執行**執行**程式、** **閒置**的執行程式，以及**最大**程式 關於已配置的執行程式，「已新增執行程式」或「已移除執行程式」事件將會使得已配置的執行程式數目增加或減少，您可以查看「作業」中的「事件時間表」以取得更多比較。

    ![sparkui 診斷執行 [] 索引標籤](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

+ 選取 [色彩] 圖示，以選取或取消選取所有草稿中的對應內容。

    ![sparkui 診斷選取圖表](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>常見問題集

### <a name="1-revert-to-community-version"></a>1. 還原為 [社區版本]

若要還原為社群版本，請執行下列步驟：

1. 在 Ambari 中開啟叢集。
1. 流覽至**Spark2** >  ** > ** **自訂 Spark2 的預設值**。
1. 選取 [**新增屬性 ...** ]，新增 [spark]。 **enabled = false**，[儲存]。
1. 屬性現在會設定為 **false**。
1. 選取 [儲存] 以儲存組態。

    ![Apache Ambari 功能關閉](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. 在左面板中選取**Spark2** ，在 [**摘要**] 索引標籤底下，選取 [ **Spark2 歷程記錄伺服器**]

    ![Apache Ambari Spark2 摘要視圖](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. 選取 [**重新開機** **Spark2 歷程記錄伺服器**] 來重新開機歷程記錄伺服器。

    ![Apache Ambari Spark2 歷程記錄重新開機](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  
1. Spark 記錄伺服器 Web UI 在重新整理後就會還原成社群版本。

### <a name="2-upload-history-server-event"></a>2. 上傳歷程記錄伺服器事件

如果您遇到記錄伺服器錯誤，請依照下列步驟來提供事件：

1. 在歷程記錄伺服器 web UI 中選取 [**下載**]，以下載事件。

    ![Spark2 歷程記錄伺服器下載](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. 從 [資料/圖表] 索引標籤選取 [**提供意見**反應]。

    ![Spark graph 提供我們意見反應](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. 提供錯誤的標題和描述，並將 zip 檔拖曳至 [編輯] 欄位中，然後按一下 [提交新問題]。

    ![apache spark 檔案問題範例](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="3-upgrade-jar-file-for-hotfix-scenario"></a>3. 升級適用于修補程式案例的 jar 檔案

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

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

**從 Azure 入口網站中使用 Bash 檔案**

1. 啟動[Azure 入口網站](https://ms.portal.azure.com)，然後選取您的叢集。
2. 使用下列參數完成[腳本動作](../hdinsight-hadoop-customize-cluster-linux.md)：

    |屬性 |Value |
    |---|---|
    |指令碼類型|- 自訂|
    |Name|UpgradeJar|
    |Bash 指令碼 URI|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |節點類型|Head、Worker|
    |參數|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Azure 入口網站提交腳本動作](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>已知問題

+ 目前，它僅適用于 Spark 2.3 和2.4 叢集。

+ 使用 RDD 的輸入/輸出資料不會顯示在 [資料] 索引標籤中。

## <a name="next-steps"></a>後續步驟

+ [在 HDInsight 上管理 Apache Spark 叢集的資源](apache-spark-resource-manager.md)
+ [設定 Apache Spark 設定](apache-spark-settings.md)

## <a name="contact-us"></a>連絡我們

如果您有任何意見反應，或在使用此工具時遇到任何問題，請在（[hdivstool@microsoft.com](mailto:hdivstool@microsoft.com)）傳送電子郵件。
