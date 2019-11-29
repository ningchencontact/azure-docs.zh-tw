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
ms.openlocfilehash: a4c911fa077c2ec332974fb2f0c5abcdef21307c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284088"
---
## <a name="general-entity-types"></a>一般實體類型：

### <a name="person"></a>人物
文字中的已辨識名稱和其他人員。
語言：
* 公開預覽： `English`

| 子類型名稱 | 描述             |
|--------------|-------------------------|
| N/A          | 識別的名稱，例如 `Bill Gates`，`Marie Curie` |

### <a name="location"></a>Location

自然和人為地標、結構和地理位置功能。

語言：


* 公開預覽： `English`

| 子類型名稱 | 描述                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/A          | 位置，例如 `Atlantic Ocean`、`library`、`Eiffel Tower`、`Statue of Liberty` |

### <a name="organization"></a>組織  

已辨識的組織、公司、機構和其他人員群組。 例如：公司、政治群組、音樂波段、運動俱樂部、政府機構和公用組織。 Nationalities 和 religions 不包含在此實體類型中。 語言： 

* 公開預覽： `English`

| 子類型名稱 | 描述                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/A          | 組織（例如 `Microsoft`） `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>電話號碼

電話號碼（僅限美國電話號碼）。 

語言：


* 公開預覽： `English`

| 子類型名稱 | 描述                                  |
|----------|----------------------------------------------|
| N/A         | 美國電話號碼，例如 `(312) 555-0176`。 |

### <a name="email"></a>電子郵件

電子郵件地址。 

語言：


* 公開預覽： `English`

| 子類型名稱 | 描述                                  |
|----------|----------------------------------------------|
| N/A         | 電子郵件地址，例如 `support@contoso.com` |

### <a name="url"></a>URL

網際網路 Url。

語言：


* 公開預覽： `English`

| 子類型名稱 | 描述                                           |
|----------|-------------------------------------------------------|
| N/A         | 網站的 Url，例如 `https://www.bing.com`。 |

###  <a name="number"></a>Number

數位和數值數量。 

語言：


* 公開預覽： `English`

| 子類型名稱    | 範例                     |
|-------------|------------------------------|
| N/A         | `6`，`six`                   |
| 百分比  | `50%`，`fifty percent`       |
| 序號     | `2nd`，`second`              |
| 貨幣    | `$10.99`，`€30.00`           |
| 維度   | `10 miles`，`40 cm`          |
| 溫度 | `32 degrees`，`10°C`         |
