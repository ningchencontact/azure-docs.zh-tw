---
title: 機器學習服務的 Azure [資料總管] 中的功能
description: 使用 machine learning 的 Azure [資料總管] 中的根本原因分析叢集。
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: bc72cc21ab525ec82d9ce4b24e80ce82d92a5d21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233508"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>機器學習服務的 Azure [資料總管] 中的功能

Azure 資料總管 中，巨量資料分析平台，可用來監視服務健全狀況、 QoS 或故障裝置使用內建的異常行為[異常偵測和預測](/azure/data-explorer/anomaly-detection)函式。 一旦偵測到異常模式時，若要減輕或解決異常執行根本原因分析 (RCA)。

診斷程序既複雜和冗長又領域專家所完成。 此程序包括擷取和加入額外的資料來自不同來源相同的時間範圍內，找出上多個維度，圖表製作額外的變數值的散發中的變更和其他技術根據定義域知識和直覺。 由於這些診斷案例是常見的 Azure [資料總管] 中，就有一個機器學習服務外掛程式可輕鬆診斷階段，並縮短 RCA 的持續時間。

Azure 的資料總管有三個機器學習服務外掛程式： [ `autocluster` ](/azure/kusto/query/autoclusterplugin)， [ `basket` ](/azure/kusto/query/basketplugin)，以及[ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin)。 所有外掛程式都實作叢集的演算法。 `autocluster`並`basket`外掛程式叢集的單一記錄集和`diffpatterns`外掛程式叢集的兩個記錄集之間的差異。

## <a name="clustering-a-single-record-set"></a>叢集的單一資料錄集

常見的案例包括資料集選取特定的準則，例如出現異常行為、 高溫度裝置、 持續時間較長的命令和消費使用者前的時間範圍。 我們想要簡單且快速的方式，來尋找資料中的常見的模式 （區段）。 模式是記錄共用相同的值，透過多個維度 （類別資料行） 的資料集的子集。 下列查詢會建置，並示範一週內的服務例外狀況的時間序列，在 10 分鐘的分類收納：

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![服務例外狀況的時間圖表](media/machine-learning-clustering/service-exceptions-timechart.png)

服務例外狀況計數相互關聯與整體服務流量。 您可以清楚看到每日的模式，如為星期一至星期五的工作日，增加服務例外狀況計數中間一天，並卸除在夜間的計數中。 週末時，會顯示一般低的計數。 可以使用來偵測例外狀況尖峰[時間序列異常偵測](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection)Azure 在資料總管中。

在資料中的第二個特殊圖文集就會發生在星期二下午上。 下列查詢用來進一步診斷此突然增加。 使用查詢來確認它是否明確的突然增加，來重繪圖表周圍突然增加，在更高的解析度 （8 小時，在一分鐘的分類收納），並檢視其框線。

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![焦點放在尖峰時間圖表](media/machine-learning-clustering/focus-spike-timechart.png)

我們看到的窄兩分鐘暴增從 15:00 到 15:02。 在下列查詢中，計算此兩分鐘視窗中的例外狀況：

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

在下列查詢中，範例 20 972 不足的例外狀況：

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | 區域 | ScaleUnit | DeploymentId                     | 追蹤點 | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>用於設定叢集的單一記錄的 autocluster()

即使有不超過一千個例外狀況，它是仍然難以尋找常見的區段，每個資料行中有多個值。 您可以使用[ `autocluster()` ](/azure/kusto/query/autoclusterplugin)立即擷取小型清單的一般區段，並尋找有趣的外掛程式叢集突然增加的兩分鐘內，如下列查詢所示：

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Count | Percent | 區域 | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

您可以看到上述結果中的最主要的區段包含 65.74%的總例外狀況記錄，並共用四個維度。 下一個區段是較不常見，包含只 9.67%的記錄，以及共用三個維度。 其他區段是更常見的。 

Autocluster 會使用專屬的演算法，採礦多維度和擷取感興趣的區段。 「 有趣 」 表示每個區段有重大的涵蓋範圍的記錄集和功能集。 也會分歧區段，而這表示每一個都與其他完全不同。 一或多個這些區段可能會與相關的 RCA 程序。 為了減少區段檢閱和評估，autocluster 會擷取小區段清單。

### <a name="use-basket-for-single-record-set-clustering"></a>用於設定叢集的單一記錄的 basket()

您也可以使用[ `basket()` ](/azure/kusto/query/basketplugin)外掛程式，如下列查詢所示：

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Count | Percent | 區域 | ScaleUnit | DeploymentId | 追蹤點 | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

購物籃實作 Apriori 演算法，以設定採礦項目，並擷取記錄集的涵蓋範圍大於閾值 （預設為 5%) 的所有區段。 您可以看到更多區段，擷取與類似的項目 （適用於範例中，區段 0，1 或 2，3）。

這兩個外掛程式是功能強大且容易使用，但其顯著的限制是因為，這些叢集不受監督方式 （沒有標籤） 中設定的單一記錄。 因此，是不太清楚的擷取的模式是描述選取的資料錄集 （異常的記錄） 或全域記錄集。

## <a name="clustering-the-difference-between-two-records-sets"></a>叢集的兩個資料錄集之間的差異

[ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin)外掛程式可克服的限制`autocluster`和`basket`。 `Diffpatterns` 採用兩個記錄集，並擷取不同，兩者之間的主要區段。 一組通常包含異常的記錄集正在調查 (一個由分析`autocluster`和`basket`)。 另一組包含參考資料錄集 （基準）。 

在下列查詢中，我們會使用`diffpatterns`突然增加的兩分鐘，不同於基準中的叢集內尋找有趣的叢集。 我們會定義 [基準] 視窗中，為八個分鐘的時間之前 15:00 （當特殊圖文集啟動）。 我們也需要指定特定的記錄所屬的基準或異常的集合將二進位資料行 (AB) 來擴充。 `Diffpatterns` 實作監督式的學習演算法，其中的兩個類別標籤所產生的異常與基準旗標 (AB)。

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | 區域 | ScaleUnit | DeploymentId | 追蹤點 |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

最主要的區段是所擷取的相同區段`autocluster`，其涵蓋範圍，在兩分鐘異常視窗還有 65.74%。 但其八分鐘基準視窗上的涵蓋範圍為僅 1.7%。 差別在於 64.04%。 這項差異似乎跟異常的高峰。 您可以將原始圖表分割成屬於這個有問題的區段與其他區段如下列查詢所示的記錄，以確認這項假設：

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![驗證 'diffpattern' 區段時間圖表](media/machine-learning-clustering/validating-diffpattern-timechart.png)

此圖表可讓我們看到在星期二下午尖峰會發生此特定區段中，使用探索到的例外狀況`diffpatterns`外掛程式。

## <a name="summary"></a>總結

Azure 資料總管 Machine Learning 外掛程式是許多情況下很有幫助。 `autocluster`和`basket`實作非監督式的學習演算法且容易使用。 `Diffpatterns` 實作監督式學習演算法雖然更複雜，它是在擷取 RCA 差異區段的功能更強大。

在特定案例中，而在近乎即時的監視服務的自動，會以互動方式使用這些增益集。 在 Azure [資料總管] 中，時間序列異常偵測後面高度最佳化，以符合必要的效能標準的診斷程序。
