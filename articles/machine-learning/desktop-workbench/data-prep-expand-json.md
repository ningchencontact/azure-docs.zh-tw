---
title: 使用 Azure Machine Learning Workbench 展開 JSON 轉換
description: 「展開 JSON」轉換的參考文件
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 21de94d2d0d3cc12aabcb8e9e8b0eec39b0a2710
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/19/2018
---
# <a name="expand-json-transformation"></a>展開 JSON 轉換
**展開 JSON**轉換可讓使用者將包含有效 JSON 文字的現有資料行展開至多個資料行。

## <a name="how-to-perform-this-transformation"></a>如何執行此轉換

請遵循下列步驟以執行此轉換：
1. 選取包含 JSON 文字的來源資料行。
2. 從 [轉換] 功能表選取 [展開 JSON]。 或者，在來源資料行的標頭上按一下滑鼠右鍵，並從操作功能表選取 [展開 JSON]。 
3. 按一下 [SERVICEPRINCIPAL] 。 
 
隨即在來源資料行旁邊新增新的資料行。 這些資料行包含 JSON 文字中下一層的屬性。 屬性索引鍵 (若有的話) 會用來建立對應資料行的名稱。 巢狀屬性將會保留為 JSON 文字，使用者可以視需要反覆展開或捨棄。

## <a name="examples"></a>範例

來源資料行 *Customer* 會展開成兩個資料行 *Customer.Name* 與 *Customer.Phone*。

| Customer                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| { "Name" : "Carrie Dodson", "Phone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| { "Name" : "Leonard Robledo", "Phone" : "456-7890-123"} | Leonard Robledo | 456-7890-123   |

