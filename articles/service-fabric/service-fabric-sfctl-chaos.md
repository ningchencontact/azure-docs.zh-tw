---
title: Azure Service Fabric CLI - sfctl choas | Microsoft Docs
description: 描述 Service Fabric CLI sfctl choas 命令。
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e4d47b1de509c2053996d9d1078733d7055447
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
啟動、停止及報告 chaos 測試服務。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
|    report| 根據傳入接續權杖或傳入的時間範圍取得 Chaos 報告的下個區段。|
|    start | 在叢集中啟動 Chaos。|
|    stop  | 停止叢集中的 Chaos (如果正在執行)，否則不會執行任何動作。|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
根據傳入接續權杖或傳入的時間範圍取得 Chaos 報告的下個區段。

您可以指定 ContinuationToken 取得混亂報告的下個區段，或者您可以透過 StartTimeUtc 與 EndTimeUtc 指定時間範圍，但您無法在同一個呼叫中同時指定 ContinuationToken 與時間範圍。 當混亂事件超過 100 個時，系統會分區段傳回混亂報告，每個區段中包含的混亂事件不超過 100 個。 

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --continuation-token| 接續權杖參數可用來取得下一組結果。 具有非空白值的接續權杖會在來自系統的結果無法放入單一回應中時，隨附在 API 的回應中。 當此值傳遞至下一個 API 呼叫時，API 會傳回下一組結果。 如果沒有任何進一步的結果，接續權杖就不會包含值。 此參數的值不能經過 URL 編碼。|
| --end-time-utc   | 表示要產生 Chaos 報告之時間範圍結束時間的 Windows 檔案時間。 如需詳細資訊，請參閱 [DateTime.ToFileTimeUtc 方法](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx)。|
| --start-time-utc | 表示要產生 Chaos 報告之時間範圍開始時間的 Windows 檔案時間。 如需詳細資訊，請參閱 [DateTime.ToFileTimeUtc 方法](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx)。|
| --timeout -t     | 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug          | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h        | 顯示此說明訊息並結束。|
| --output -o      | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query          | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose        | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
在叢集中啟動 Chaos。

如果 Chaos 尚未在叢集中執行，此命令會使用傳入的 Chaos 參數啟動 Chaos。 如果在進行這個呼叫時 Chaos 已在執行中，則呼叫會失敗，錯誤碼為 FABRIC_E_CHAOS_ALREADY_RUNNING。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --app-type-health-policy-map  | 對於特定應用程式類型，有最大健康情況不良應用程式百分比的 JSON 編碼清單。 每個項目都會指定應用程式類型名稱做為一個索引鍵，並指定一個整數的值，表示用來評估指定應用程式類型之應用程式的 MaxPercentUnhealthyApplications 百分比。 對於特定應用程式類型，定義具有最大健康情況不良應用程式百分比的對應。 每個項目都會指定應用程式類型名稱做為一個索引鍵，並指定一個整數的值，表示用來評估指定應用程式類型之應用程式的 MaxPercentUnhealthyApplications 百分比。 應用程式類型的健康狀態原則對應可以在叢集健康狀態評估期間，用來描述特殊的應用程式類型。 對應中包含的應用程式類型是針對在對應中指定的百分比進行評估，不包含在叢集健康原則中定義的全域 MaxPercentUnhealthyApplications。 對應中指定之應用程式類型的應用程式不會針對應用程式的全域集區進行計算。 例如，如果某個類型的某些應用程式非常重要，叢集系統管理員可以針對該應用程式類型將項目新增至對應，並且為它指派 0% 的值 (也就是不容忍任何失敗)。 其他所有應用程式則可以使用設為 20% 的 MaxPercentUnhealthyApplications 來進行評估，以容忍數千個應用程式執行個體發生一些失敗。 只有在叢集資訊清單使用 HealthManager/EnableApplicationTypeHealthEvaluation 的設定項目啟用應用程式類型健康情況評估時，才會使用應用程式類型健康原則對應。|
|--chaos-target-filter         | 具有兩個字串類型索引鍵的 JSON 編碼字典。 這兩個索引鍵分別是 NodeTypeInclusionList 和 ApplicationInclusionList。 這兩個索引鍵的值都是字串清單。 chaos_target_filter 會針對鎖定的 Chaos 錯誤定義所有篩選，例如，只有特定節點類型或特定應用程式會引發錯誤。 如果未使用 chaos_target_filter，Chaos 會針對所有叢集實體引發錯誤。 如果使用 chaos_target_filter，則 Chaos 只會針對符合 chaos_target_filter 規格的實體引發錯誤。 NodeTypeInclusionList 和 ApplicationInclusionList 只允許集合聯集語意。 您不能指定 NodeTypeInclusionList 和 ApplicationInclusionList 的交集。 例如，您不能指定「只有在此應用程式位於該節點類型上時才對它引發錯誤」。 若 NodeTypeInclusionList 或 ApplicationInclusionList 中包含某個實體，則該實體就不能使用 ChaosTargetFilter 排除。 即使 applicationX 並未出現在 ApplicationInclusionList 中，但在某些混亂反覆項目中也能對 applicationX 引發錯誤，因為它正好位於包括在 NodeTypeInclusionList 中的 nodeTypeY 的節點上。 如果 NodeTypeInclusionList 和 ApplicationInclusionList 都是空白，則會擲回 ArgumentException。 針對這些節點類型的節點，會啟用所有錯誤類型 (重新啟動節點、重新啟動程式碼封裝、移除複本、重新啟動複本、移動主要複本，以及移動次要複本)。 如果某個節點類型 (假設為 NodeTypeX) 未出現在 NodeTypeInclusionList 中，則系統將永遠不會針對 NodeTypeX 的節點啟用節點層級錯誤 (例如 NodeRestart)，但如果 ApplicationInclusionList 中的某個應用程式正好位於 NodeTypeX 的節點上，則仍會針對 NodeTypeX 啟用程式碼套件和複本錯誤。 您最多可在此清單中包含 100 個節點類型名稱，若要增加此數目，則必須針對 MaxNumberOfNodeTypesInChaosEntityFilter 設定進行設定升級。 屬於這些應用程式之服務的所有複本，都適用於由混亂所引發的複本錯誤 (重新啟動複本、移除複本、移動主要複本和移動次要複本)。 只有在程式碼封裝只裝載這些應用程式的複本時，混亂才可以將程式碼封裝重新啟動。 如果某個應用程式未出現在此清單中，系統仍然可能在某些混亂反覆項目中針對它引發錯誤，前提是該應用程式位於某個包含在 NodeTypeInclusionList 中之節點類型的節點上。 不過，如果 applicationX 因位置限制而繫結至 nodeTypeY，且 applicationX 未出現在 ApplicationInclusionList，而且 nodeTypeY 未出現在 NodeTypeInclusionList 中，則系統將永遠不會對 applicationX 引發錯誤。 您最多可在此清單中包含 1000 個應用程式名稱，若要增加此數目，則必須針對 MaxNumberOfApplicationsInChaosEntityFilter 設定進行設定升級。|
|--context                     | (string, string) 類型之索引鍵-值組的 JSON 編碼對應。 此對應可用於記錄混亂執行的相關資訊。 此類組合不能超過 100 個，且每個字串 (索引鍵或值) 最多為 4095 個字元長。 此對應由混亂執行的起始者設定，以選擇性地儲存特定執行的相關內容。|
| --disable-move-replica-faults | 停用移動主要錯誤和移動次要錯誤。|
| --max-cluster-stabilization| 等待所有叢集實體變成穩定且健康情況良好的最大時間量。  預設值：60。 Chaos 會在反覆項目中執行，並且在每個反覆項目開始時它會驗證叢集實體的健康情況。 在驗證期間，如果叢集實體在 MaxClusterStabilizationTimeoutInSeconds 內不穩定且狀況不良，Chaos 就會產生驗證失敗事件。|
| --max-concurrent-faults    | 每個反覆運算引發的最大並行錯誤數。           預設值：1。 Chaos 會在反覆項目中執行，兩個連續的反覆項目會以驗證階段分隔。 並行越高，錯誤的插入就會越積極 -- 包括更複雜的狀態系列以發現錯誤。 建議從值為 2 或 3 開始，上調此值時請小心謹慎。|
| --max-percent-unhealthy-apps  | 在 Chaos 期間評估叢集健康情況時，在報告錯誤之前，允許健康情況不良應用程式的最大百分比。 在報告錯誤之前，允許健康情況不良應用程式的最大百分比。 例如，若要允許 10% 的應用程式健康情況不良，這個值會是 10。 百分比表示在系統將叢集視為錯誤之前，容許應用程式健康情況不良的最大百分比。 如果未到達此百分比，但至少有一個健康情況不良的應用程式，則健康情況會評估為 Warning。 此計算是將健康情況不良的應用程式數目除以叢集中應用程式執行個體的總數而得，但不包括 ApplicationTypeHealthPolicyMap 所包含之應用程式類型的應用程式。 針對少量的應用程式數目，計算會四捨五入以容許一個失敗。 預設百分比是零。|
| --max-percent-unhealthy-nodes | 在 Chaos 期間評估叢集健康情況時，在報告錯誤之前，允許健康情況不良節點的最大百分比。 在報告錯誤之前，允許健康情況不良節點的最大百分比。 例如，若要允許 10% 的節點健康情況不良，這個值會是 10。 百分比表示在叢集被視為處於錯誤狀態之前，容許節點健康情況不良的最大百分比。 如果未到達此百分比，但至少有一個健康情況不良的節點，則健康情況會評估為 Warning。 百分比是將健康情況不良節點數目除以叢集中的節點總數計算而得。 針對較少的節點數目，計算會四捨五入以容許一個失敗。 預設百分比是零。 在大型叢集中，永遠都有一些節點會關閉或需要修復，因此應設定此百分比來容許這種情形。|
| --time-to-run              | 在自動停止之前，Chaos 將執行的總時間 (秒)。 最大允許的值為 4,294,967,295 (System.UInt32.MaxValue)。  預設值：4294967295。|
| --timeout -t               | 伺服器逾時 (秒)。  預設值：60。|
| --wait-time-between-faults | 單一反覆運算中連續錯誤之間的等候時間 (秒)。  預設值：20。 值越大，錯誤之間的重疊越低，且叢集歷經的狀態轉換順序會越簡單。 建議從 1 到 5 之間的值來開始，上調此值時請小心謹慎。|
| --wait-time-between-iterations| Chaos 的兩次連續反覆運算之間的時間區隔 (秒)。 值越大，錯誤插入率越低。 預設值︰30。|
| --warning-as-error         | 在 Chaos 期間評估叢集健康情況時，將有相同嚴重性的警告視為錯誤。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug                    | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h                  | 顯示此說明訊息並結束。|
| --output -o                | 輸出格式。  允許的值：json、jsonc、table、tsv。           預設值：json。|
| --query                    | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose                  | 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
停止叢集中的 Chaos (如果正在執行)，否則不會執行任何動作。

阻止 Chaos 排程進一步的錯誤；但不會影響執行中的錯誤。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --timeout -t| 伺服器逾時 (秒)。  預設值：60。|

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug  | 增加記錄詳細程度以顯示所有偵錯記錄。|
| --help -h| 顯示此說明訊息並結束。|
| --output -o | 輸出格式。  允許的值：json、jsonc、table、tsv。  預設值：json。|
| --query  | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http://jmespath.org/。|
| --verbose| 增加記錄詳細程度。 使用 --debug 取得完整偵錯記錄。|

## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](/azure/service-fabric/scripts/sfctl-upgrade-application)來使用 Service Fabric CLI。