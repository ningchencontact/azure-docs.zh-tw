---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132776"
---
### <a name="cacheskuname"></a>cacheSKUName

新的 Azure Cache for Redis 的定價層。

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

範本定義 （Basic、 Standard 或 Premium），此參數允許的值，並指派預設值 （基本），如果未不指定任何值。 「基本」提供單一節點，有多種大小可用，最大為 53 GB。 「標準」提供雙節點「主要/複本」，有多種大小可用，最大為 53 GB，還有高達 99.9% 的 SLA。

### <a name="cacheskufamily"></a>cacheSKUFamily

Sku 系列。

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

新 Azure Cache for Redis 執行個體的大小。

基本和標準的系列：

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

定義進階值快取容量是一樣的只允許的值從 1 跑到 5 而不是從 0 到 6。

範本會定義可用於此參數 （0 到 6 個用於基本和標準系列; 1 到 5 個 Premium 系列） 的整數值。 如果未不指定任何值，範本會指派預設值為 0，針對基本和標準，premium 1。

值會對應到下列快取大小：

| 值 | 基本和標準<br>快取大小 | 進階<br>快取大小 |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB （預設值）                 | n/a                   |
| 1     | 1 GB                             | 6 GB （預設值）        |
| 2     | 2.5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | n/a                   |
