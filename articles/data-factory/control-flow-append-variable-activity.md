---
title: Azure Data Factory 中的附加變數活動
description: 了解如何設定「附加變數」活動，以將值新增至 Data Factory 管線中定義的現有陣列變數
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679983"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory 中的附加變數活動

使用「附加變數」活動，將值新增至 Data Factory 管線中定義的現有陣列變數。

## <a name="type-properties"></a>類型屬性

屬性 | 說明 | 必要
-------- | ----------- | --------
名稱 | 管線中的活動名稱 | 是
說明 | 說明活動用途的文字 | no
類型 | 活動類型是 AppendVariable | 是
value | 用來附加到指定變數的字串常值或運算式物件值 | 是
variableName | 將由活動修改的變數名稱，該變數必須是「陣列」類型 | 是

## <a name="next-steps"></a>後續步驟
深入了解 Data Factory 所支援的相關控制流程活動： 

- [設定變數活動](control-flow-set-variable-activity.md)
