---
title: Azure Data Factory 中的附加變數活動 | Microsoft Docs
description: 了解如何設定「附加變數」活動，以將值新增至 Data Factory 管線中定義的現有陣列變數
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575149"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory 中的附加變數活動

使用「附加變數」活動，將值新增至 Data Factory 管線中定義的現有陣列變數。

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 必要項
-------- | ----------- | --------
name | 管線中的活動名稱 | 是
說明 | 說明活動用途的文字 | no
type | 活動類型是 AppendVariable | 是
value | 用來附加到指定變數的字串常值或運算式物件值 | 是
variableName | 將由活動修改的變數名稱，該變數必須是「陣列」類型 | 是

## <a name="next-steps"></a>後續步驟
深入了解 Data Factory 所支援的相關控制流程活動： 

- [設定變數活動](control-flow-set-variable-activity.md)
