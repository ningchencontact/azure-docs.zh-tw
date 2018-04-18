---
title: 適用於 Azure 資源的 Privileged Identity Management - 合格指派和資源可見度 | Microsoft Docs
description: 說明如何將成員指派為「合格」的資源角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>合格指派和資源可見度

適用於 Azure 資源的 PIM 角色可為具有重要 Azure 資源的組織，提供增強式安全性。 PIM 讓資源管理員能夠將成員指派為合格的資源角色。 深入了解不同的指派類型和下列 Azure 資源角色的狀態。 

## <a name="assignment-types"></a>指派類型

適用於 Azure 資源的 PIM 提供兩種不同的指派類型：

- 合格
- Active

合格的指派會要求角色成員先執行某個動作才能使用此角色。 這些動作可能包含成功執行多重要素驗證檢查、提供理由，以及向指定的核准者要求核准。

有效的指派不要求成員先執行某個動作才能使用此角色。 指派為「有效」的成員隨時具有角色所提供的權限。

## <a name="assignment-duration"></a>指派持續時間

資源管理員可以在進行角色的 PIM 設定時，針對每個指派類型選擇兩個選項的其中一個。 將成員指派給 PIM 中的角色後，下列選項才會變成預設最長持續時間。

- 允許永久合格的指派
- 允許永久有效的指派

或

- 合格指派的有效期限
- 有效指派的有效期限

如果資源管理員選擇 [允許永久合格的指派] 和/或 [允許永久有效的指派]，將成員指派給此資源的所有管理員都將具備指派永久成員資格的能力。

選擇 [合格指派的有效期限] 和/或 [有效指派的有效期限] 即可控制指派生命週期，做法是要求所有的指派都具有指定的開始和結束日期。

>[!NOTE] 
>資源管理員可以更新具有指定結束日期的所有指派，而成員可以起始自助式要求來[延長或更新指派](pim-resource-roles-renew-extend.md)。


## <a name="assignment-states"></a>指派狀態

適用於 Azure 資源的 PIM 有兩個不同的指派狀態，這兩個狀態會顯示在 PIM 的 [我的角色]、[角色] 和 [成員] 檢視中的 [有效角色] 索引標籤上。 這些狀態包括：

- 已指派
- 已啟用

檢視 [有效角色] 中所列的成員資格時，[狀態] 資料行可讓您區分「已指派」為有效的使用者與「已啟用」合格指派且現在有效的使用者。

## <a name="next-steps"></a>後續步驟

[在 PIM 中指派角色](pim-resource-roles-assign-roles.md)

