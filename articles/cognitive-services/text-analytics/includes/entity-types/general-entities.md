---
title: 一般命名實體
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: e81a1de02c112abd5c52f0f83404a615d738e01c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501076"
---
## <a name="general-entity-types"></a>一般實體類型：

### <a name="person"></a>Person
文字中的已辨識名稱和其他人員。
語言：
* 公開預覽： `English`

| 子類型名稱 | 說明             |
|--------------|-------------------------|
| N/A          | 識別的名稱，例如 `Bill Gates`，`Marie Curie` |

### <a name="location"></a>位置

自然和人為地標、結構和地理位置功能。

語言：


* 公開預覽： `English`

| 子類型名稱 | 說明                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/A          | 位置，例如 `Atlantic Ocean`、`library`、`Eiffel Tower`、`Statue of Liberty` |

### <a name="organization"></a>組織  

已辨識的組織、公司、機構和其他人員群組。 例如：公司、政治群組、音樂波段、運動俱樂部、政府機構和公用組織。 Nationalities 和 religions 不包含在此實體類型中。 語言： 

* 公開預覽： `English`

| 子類型名稱 | 說明                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/A          | 組織（例如 `Microsoft`） `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>電話號碼

電話號碼。 

語言：


* 公開預覽： `English`

| 子類型名稱 | 說明                                  |
|----------|----------------------------------------------|
| N/A         | 電話號碼，例如 `+1 123-123-123`。 |

### <a name="url"></a>URL

網際網路 Url。

語言：


* 公開預覽： `English`

| 子類型名稱 | 說明                                           |
|----------|-------------------------------------------------------|
| N/A         | 網站的 Url，例如 `https://www.bing.com`。 |

###  <a name="number"></a>Number

數位和數值數量。 

語言：


* 公開預覽： `English`

| 子類型名稱    | 範例                     |
|-------------|------------------------------|
| N/A         | `6`、`six`                   |
| 百分比  | `50%`、`fifty percent`       |
| 序數     | `2nd`、`second`              |
| 貨幣    | `$10.99`、`€30.00`           |
| 維度   | `10 miles`、`40 cm`          |
| 溫度 | `32 degrees`、`10°C`         |
