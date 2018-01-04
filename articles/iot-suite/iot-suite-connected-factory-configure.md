---
title: "設定連接的工廠拓撲 |Microsoft 文件"
description: "如何設定連接工廠的拓樸預先設定的解決方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 19e0f48ab817428a1f953c80296b2e23effe5a8a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="configure-the-connected-factory-preconfigured-solution"></a>設定連接的工廠預先設定的解決方案

預先設定連接的工廠方案顯示虛構公司 Contoso 模擬儀表板。 此公司許多全域位置中已處理站的全域。

描述如何設定連接的工廠解決方案的拓撲，本文會使用 Contoso 做為範例。

## <a name="simulated-factories-configuration"></a>模擬的處理站組態

每個 Contoso 工廠有三個站台所組成的實際執行線。 每個站台是實際的 OPC UA 伺服器與特定角色：

* 組件站台
* 測試站台
* 封裝的站台

這些 OPC UA 伺服器有 OPC UA 節點和[OPC 發行者](https://github.com/Azure/iot-edge-opc-publisher)會將這些節點的值傳送至連接的 factory。 其中包括：

* 例如目前的功率耗用量的目前操作狀態。
* 所產生的實際執行環境資訊，例如產品數目。

您可以使用儀表板，Contoso factory 拓樸，從全域檢視站台層級檢視向下鑽研。 連線的 factory 儀表板可讓您︰

* 針對拓撲中每個圖層的 OEE 和 KPI 圖形的視覺效果。
* 在站台的 OPC UA 節點的目前值的視覺效果。
* OEE 和 KPI 圖形的站台層級從全域層級彙總。
* 警示和值到達特定閾值時所要執行動作的視覺效果。

## <a name="connected-factory-topology"></a>連線的 factory 拓樸

處理站、 生產線條，以及站台的拓撲是階層式：

* 全域層級有 factory 節點做為子系。
* 處理站有生產線節點做為子系。
* 生產線具有工作站節點做為子系。
* 站台 （OPC UA 伺服器） 具有 OPC UA 節點做為子系。

拓撲中的每個節點有一組共同定義的屬性：

* 拓撲節點唯一識別碼。
* 的名稱。
* 描述。
* 映像。
* 拓撲節點的子系。
* 最小值、 目標和 OEE 和 KPI 數字和警示的動作執行的最大值。

## <a name="topology-configuration-file"></a>拓撲組態檔

若要設定上一節中所列的屬性，已連線的 factory 解決方案會使用組態檔稱為[ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json)。

您可以找到這個檔案中的方案原始程式碼中`WebApp/Contoso/Topology`資料夾。

下列程式碼片段顯示的外框`ContosoTopologyDescription.json`組態檔：

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

通用屬性`<global_configuration>`， `<factory_configuration>`， `<production_line_configuration>`，和`<station_configuration>`是：

* **名稱**（輸入字串）

  定義的描述性名稱，它應該只有一個拓撲節點，以在儀表板中顯示文字。

* **描述**（輸入字串）

  描述更詳細的拓撲節點。

* **映像**（輸入字串）

  WebApp 方案以顯示當拓撲節點有關的資訊會顯示儀表板中的映像的路徑。

* **OeeOverall**， **OeePerformance**， **OeeAvailability**， **OeeQuality**， **Kpi1**， **Kpi2**(型別`<performance_definition>`)

  這些屬性定義最小、 目標和最大值的作業圖用來產生警示。 這些屬性也會定義如果偵測到警示時執行的動作。

`<factory_configuration>`和`<production_line_configuration>`項目具有屬性：

* **Guid** （輸入字串）

  唯一識別拓撲節點。

`<factory_configuration>`具有屬性：

* **位置**(型別`<location_definition>`)

  指定之處理站所在的位置。

`<station_configuration>`具有屬性：

* **OpcUri** （輸入字串）

  這個屬性必須設定的 OPC UA 伺服器 OPC UA 應用程式的 uri。
  它必須是全域唯一 OPC UA 規格，因為這個屬性用來識別站台拓撲節點。

* **OpcNodes**，其為 OPC UA 節點陣列 (型別`<opc_node_description>`)

`<location_definition>`具有屬性：

* **縣 （市)** （輸入字串）

  最接近位置城市的名稱

* **國家/地區**（輸入字串）

  位置的國家/地區

* **緯度**（double 類型）

  位置的緯度

* **經度**（double 類型）

  經度的位置

`<performance_definition>`具有屬性：

* **最小值**（double 類型）

  可達到臨界值下限值。 如果目前的值是低於此閾值，則會產生警示。

* **目標**（double 類型）

  理想的目標值。

* **最大**（double 類型）

  可能會達到上限臨界值的值。 如果目前的值高於此臨界值，則會產生警示。

* **MinimumAlertActions** (型別`<alert_action>`)

  定義可以做為最小的警示回應採取的動作集合。

* **MaximumAlertActions** (型別`<alert_action>`)

  定義可以做為最大警示回應採取的動作集合。

`<alert_action`> 具有屬性：

* **型別**（輸入字串）

  警示的動作類型。 已知下列類型：

  * **AcknowledgeAlert**： 警示的狀態應該變更為已認可。
  * **CloseAlert**： 相同類型的所有較舊的警示不會再應該會顯示在儀表板。
  * **CallOpcMethod**: OPC UA 方法應該呼叫。
  * **OpenWebPage**： 應開啟瀏覽器視窗，顯示其他內容的相關資訊。

* **描述**（輸入字串）

  儀表板中顯示的動作描述。

* **參數**（輸入字串）

  若要執行此動作所需的參數。 值取決於動作類型。

  * **AcknowledgeAlert**： 沒有所需的參數。
  * **CloseAlert**： 沒有所需的參數。
  * **CallOpcMethod**： 呼叫格式 」 的父節點，節點識別碼的方法呼叫，OPC UA 伺服器 URI 的 NodeId。 」 的 OPC UA 方法的參數與節點資訊
  * **OpenWebPage**： 要在瀏覽器視窗中顯示的 URL。

`<opc_node_description>`包含 OPC UA 節點中的站台 （OPC UA 伺服器） 的相關資訊。 代表沒有現有的 OPC UA 節點，但會做為儲存體連接的原廠的計算邏輯中的節點也是有效的。 它有下列屬性：

* **NodeId** （輸入字串）

  OPC UA 工作站 （OPC UA 伺服器的） 中的節點位址空間的位址。 必須是語法 NodeId 的 OPC UA 規格中所指定。

* **SymbolicName** （輸入字串）

  顯示這個 OPC UA 節點的值時，儀表板中顯示的名稱。

* **相關性**（string 型別陣列）

  表示 OEE 或 KPI OPC UA 節點值的計算是相關。 每個陣列元素可以是下列值之一：

  * **OeeAvailability_Running**: OEE 可用性的計算值無關。
  * **OeeAvailability_Fault**: OEE 可用性的計算值無關。
  * **OeePerformance_Ideal**： 值來計算 OEE 效能相關，而且通常是常數值。
  * **OeePerformance_Actual**: OEE 效能的計算值無關。
  * **OeeQuality_Good**: OEE 品質的計算值無關。
  * **OeeQuality_Bad**: OEE 品質的計算值無關。
  * **Kpi1**: KPI1 的計算值無關。
  * **Kpi2**: KPI2 的計算值無關。

* **OpCode** （輸入字串）

  表示 OPC UA 節點的值在時間序列深入了解查詢和 OEE/KPI 計算中的處理方式。 每個時間序列深入了解查詢的目標特定的時間範圍，其查詢的參數，並提供結果。 OpCode 控制項如何結果計算，而且可以是下列值之一：

  * **Diff**： 範圍中的最後一個及第一個值之間差異。
  * **Avg**： 範圍中的所有值的平均值。
  * **Sum**： 範圍中的所有值的總和。
  * **最後一個**： 目前未使用。
  * **計數**： 範圍中值的數目。
  * **最大**： 中範圍的最大值。
  * **Min**： 中範圍的最小值。
  * **Const**： 結果是 ConstValue 屬性所指定的值。
  * **SubMaxMin**： 最大和最小的值之間的差異。
  * **Timespan**: timespan。

* **單位**（輸入字串）

  在儀表板中定義之值的顯示單位。

* **可見**（輸入布林值）

  控制是否應該在儀表板中顯示的值。

* **ConstValue** （double 類型）

  如果**OpCode**是**Const**，則這個屬性為節點的值。

* **最小值**（double 類型）

  如果目前的值低於此值，則會產生最小的警示。

* **最大**（double 類型）

  如果目前的值高於此值引發時，就會產生最大的警示。

* **MinimumAlertActions** (型別`<alert_action>`)

  定義可以做為最小的警示回應採取的動作集合。

* **MaximumAlertActions** (型別`<alert_action>`)

  定義可以做為最大警示回應採取的動作集合。

在站台層級中，您也會看到**模擬**物件。 這些物件只會用來設定連接的工廠模擬，而且不應該用來設定實際的拓撲。

## <a name="how-the-configuration-data-is-used-at-runtime"></a>組態資料在執行階段的使用方式

使用組態檔中的所有內容可以都分為不同的分類，根據使用方式。 這些類別如下：

### <a name="visual-appearance"></a>視覺外觀

此類別中的屬性會定義連接的工廠儀表板的視覺外觀。 範例包括：

* 名稱
* 說明
* 映像
* 位置
* Units
* 可見

### <a name="internal-topology-tree-addressing"></a>內部的拓樸樹狀定址

WebApp 維護內部的資料字典，其中包含所有拓撲節點的資訊。 屬性**Guid**和**OpcUri**用做為索引鍵來存取此字典，而且需要是唯一的。

### <a name="oeekpi-computation"></a>OEE/KPI 計算

連線的 factory 模擬 OEE/KPI 數字是由參數化：

* 要包含在計算的 OPC UA 節點值。
* 如何從遙測值計算圖。

連線的 factory 會使用 OEE 公式，如 http://oeeindustrystandard.oeefoundation.org 所發行。

OPC UA 節點物件中站台啟用 OEE/KPI 計算中的使用方式的標記。 **相關性**屬性會指出哪些 OEE/KPI 圖對於應該使用 OPC UA 節點值。 **OpCode**屬性會定義如何計算中是否包含的值。

### <a name="alert-handling"></a>警示的處理

已連線的處理站都會支援簡單最小/最大閾值型警示產生機制。 有數個預先定義的動作來回應這些警示，您可以設定。 下列屬性會控制這項機制：

* 最大值
* 最小值
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>相互關聯至遙測資料

某些作業，例如視覺化的最後一個值，或建立時間序列深入了解查詢 WebApp 需要內嵌的遙測資料的定址配置。 遙測傳送至已連線的處理站也必須儲存在內部資料結構。 啟用這些作業的兩個屬性是在站台 （OPC UA 伺服器） 和 OPC UA 節點層級：

* **OpcUri**

  識別 （全域唯一的） OPC UA 伺服器遙測來自。 在內嵌的訊息中，這個屬性會以傳送**ApplicationUri**。

* **節點識別碼**

  識別 OPC UA 伺服器中的節點值。 屬性的格式必須為 OPC UA 規格中所指定。 在內嵌的訊息中，這個屬性會以傳送**NodeId**。

請檢查[這](https://github.com/Azure/iot-edge-opc-publisher)GitHub 頁面，如需如何遙測資料會內嵌至連接使用 OPC 發行者的 factory。

## <a name="example-how-kpi1-is-calculated"></a>範例： 如何計算 KPI1

中的組態`ContosoTopologyDescription.json`檔案可讓您控制 OEE/KPI 圖形的計算方式。 下列範例會示範這個檔案中的屬性如何控制 KPI1 的計算。

在已連接 KPI1 用來測量的數目已成功處理站會製造產品在過去一小時。 連線的 factory 模擬中的每個站台 (OPC UA server) 提供 OPC UA 節點 (`NodeId: "ns=2;i=385"`)，這樣會提供用來計算此 KPI 遙測。

這個 OPC UA 節點的組態看起來像下列程式碼片段：

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

此設定可啟用此節點中使用時間序列 Insights 遙測值的查詢。 時間序列 Insights 查詢會擷取：

* 值數目。
* 最小的值。
* 最大的值。
* 所有值的平均值。
* 所有的所有值的總和唯一**OpcUri** (**ApplicationUri**)， **NodeId**組中給定 timespan。

一項特性**NumberOfManufactureredProducts**節點值時，它只會增加。 若要計算的 timespan，在製造的產品數目連接處理站會使用**OpCode** **SubMaxMin**。 計算擷取的時間範圍開頭的最小值和結束的時間範圍的最大值。

**OpCode**組態中設定的計算邏輯，來計算結果的最大和最小值的差異。 然後這些結果會累積到根 （全域） 層級的下和儀表板中顯示。

## <a name="next-steps"></a>後續步驟

建議採取的後續步驟，是瞭解如何[在 Windows 或 Linux 上部署連線處理站預先設定解決方案的閘道](iot-suite-connected-factory-gateway-deployment.md)。