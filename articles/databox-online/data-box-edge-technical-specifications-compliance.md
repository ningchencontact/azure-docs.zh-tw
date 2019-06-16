---
title: Microsoft Azure 資料方塊邊緣的技術規格和合規性 |Microsoft Docs
description: 了解的技術規格和您的 Azure 資料方塊邊緣的合規性
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60755129"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Azure 資料方塊邊緣的技術規格

您的 Microsoft Azure 資料方塊邊緣裝置的硬體元件符合技術規格和這篇文章中所述的法規標準。 技術規格描述電源供應器單元 (PSUs)、 儲存體容量、 機箱和環境標準。 

## <a name="power-supply-unit-specifications"></a>電源供應器單位規格

資料方塊的邊緣裝置有兩個 100-240 V 電源供應器單元 (PSUs) 與高效能的愛用者。 兩個 PSUs 提供備援電源設定。 如果 psu 已運轉失敗時，裝置會繼續其他 psu 已運轉上正常運作，直到更換故障的模組為止。 下表列出 PSUs 的技術規格。

| 規格           | 750 W PSU 已運轉                  |
|-------------------------|----------------------------|
| 最大輸出電力    | 750 W                     |
| 頻率               | 50/60 Hz                   |
| 選取電壓範圍 | 自動設定範圍：100-240 V AC |
| 隨插即用           | 是                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>儲存體規格

資料方塊的邊緣裝置具有 10 X 2.5"NVMe Ssd，每個都具有 1.6 TB 的容量。 使用的這些 Ssd，2 作業系統磁碟，而其他 8 的資料磁碟。 裝置的可用容量總計約為 12.5 TB。 下表包含裝置的儲存體容量的詳細資料。

|     規格                          |     值             |
|--------------------------------------------|-----------------------|
|    固態硬碟 (SSD) 數     |    8                  |
|    單一 SSD 容量                     |    1.6 TB             |
|    總容量                          |    12.8 TB            |
|    可用容量總計*                  |    ~ 12.5 TB            |

**供內部使用，會保留一些空間。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>機箱尺寸和重量規格

下表列出各種機箱的尺寸和重量規格。

### <a name="enclosure-dimensions"></a>機箱尺寸

下表列出機箱尺寸 (以公釐和英吋為單位)。

|     機箱     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    44.45            |    1.75"          |
|    寬度          |    434.1           |    17.09"          |
|    長度          |    740.4           |    29.15"          |

下表列出傳送套件以公釐和英吋為單位的維度。

|     封裝     |     公釐     |     英吋     |
|-------------------|---------------------|----------------|
|    高度         |    311.2            |    12.25"          |
|    寬度          |    642.8          |    25.31"          |
|    長度          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>機箱重量

裝置套件充分權衡 66 lbs。 需要兩個人才能處理它。 裝置的重量的機箱組態而定。

|     機箱                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    包含封裝的總重量       |    61 lbs。          |
|    裝置的重量                       |    35 lbs。          |

## <a name="enclosure-environment-specifications"></a>機箱環境規格

此區段會列出機箱環境，例如溫度、 溼度和高度相關的規格。

### <a name="temperature-and-humidity"></a>溫度和溼度

|     機箱         |     周圍溫度範圍     |     周圍相對溼度     |     最大露點     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    運作        |    10°C - 35°C (50°F - 86°F)         |    10%-80%非-您縮小。         |    29°C (84°F)            |
|    無法運作    |    -40 ° C 65 ° c (-40 ° F-149 ° F)     |    5%-95%非-您縮小。          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>氣流、海拔高度、震盪、振動、方向、安全和 EMC

|     機箱                           |     運作規格                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    氣流                              |    系統氣流是從前面到後面。 系統必須以低壓、尾段排氣安裝操作。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最大運作的海拔        |    取決於 3048 公尺 （10,000 英呎） 的最大運作溫度取消分級[運作溫度取消評等規格](#operating-temperature-de-rating-specifications)。                                                                                |
|    最大高度，無法運作    |    12,000 公尺 （39,370 英呎）                                                                                                                                                                                         |
|    可運作的震盪                   |    6g 11 毫秒，以 6 的方向                                                                                                                                                                         |
|    無法運作的震盪               |    在 6 個方向中的 2 毫秒 71 G                                                                                                                                                                           |
|    可運作的振動               |    0.26 G<sub>RMS</sub> 5 的 Hz 到 350 Hz 隨機                                                                                                                                                                                     |
|    無法運作的振動           |    1.88 G<sub>RMS</sub> 10 的 Hz 到 500 Hz 15 分鐘 （所有六個邊測試。）                                                                                                                                                  |
|    定位和掛接             |    19"機架掛接                                                                                                                                                                                        |
|    安全與核可                 |    EN 60950-1:2006 + A1:2010 + A2:2013 + A11:2009 + A12:2011 / IEC 60950-1:2005 ed2 + A1:2009 A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A、 SERVICES 003 <br>EN 55032:2012 / CISPR 32:2012  <br>EN 55032:2015 / CISPR 32:2015  <br>EN 55024:2010 + A1:2015 / CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 （類別 D）   <br>EN 61000-3-3:2013 / IEC 61000-3-3:2013                                                                                                                                                                                         |
|    能源             |    佣金法規 (EU) [否]。 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>取消評等規格的運作溫度

|     操作溫度取消評等     |     周圍溫度範圍                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    最多 35 ° C (95 ° F)                       |    最大的溫度會降低由 1 ° C/300 m （1 ° F/547 英呎） 上方 950 m （3,117 英呎）。    |
|    35 ° C 到 40 ° C (95 ° F 到 104 ° F)            |    最大的溫度會降低由 1 ° C/175 m （1 ° F/319 英呎） 上方 950 m （3,117 英呎）。    |
|    40 ° C 45 ° c (104 ° F 到 113 ° F)           |    最大的溫度會降低由 1 ° C/125 m （1 ° F/228 英呎） 上方 950 m （3,117 英呎）。    |


## <a name="next-steps"></a>後續步驟

- [部署 Azure Data Box Edge](data-box-edge-deploy-prep.md)
