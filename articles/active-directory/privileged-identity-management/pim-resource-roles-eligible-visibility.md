---
title: 適用於 Azure Privileged Identity Management 的合格指派和資源可見度 | Microsoft Docs
description: 說明使用 PIM 時如何將成員指派為「合格」的資源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 336453c1ef6ef8d0295d00f31afc6a5e7e42e8b6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621543"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Privileged Identity Management 適用的合格指派和資源可見度

適用於 Azure 資源角色的 Privileged Identity Management (PIM) 可為具有重要 Azure 資源的組織，提供增強式安全性。 資源管理員可以使用 PIM 將成員指派為合格的資源角色。 深入了解不同的指派類型和下列各節中 Azure 資源角色的指派狀態。 

## <a name="assignment-types"></a>指派類型

適用於 Azure 資源的 PIM 提供兩種不同的指派類型：

- 合格
- Active

合格的指派會要求角色成員先執行某個動作才能使用此角色。 動作可能包含成功執行多重要素驗證檢查、提供業務理由，或是向指定的核准者要求核准。

有效的指派不要求成員先執行某個動作才能使用此角色。 指派為有效的成員隨時具有指派給角色的權限。

## <a name="assignment-duration"></a>指派持續時間

資源管理員可以在進行角色的 PIM 設定時，針對每個指派類型從兩個選項中選擇。 將成員指派給 PIM 中的角色後，下列選項才會變成預設最長持續時間。 

管理員可以從下列指派類型中選擇一個：

- 允許永久合格的指派
- 允許永久有效的指派

或者管理員可以從下列指派類型中選擇一個：

- 合格指派的有效期限
- 有效指派的有效期限

如果資源管理員選擇 [允許永久合格的指派] 或 [允許永久有效的指派]，將成員指派給此資源的所有管理員都可以指派永久的成員資格。

如果資源管理員選擇 [合格指派的有效期限] 或 [有效指派的有效期限]，資源管理員即可控制指派生命週期，做法是要求所有的指派都具有指定的開始和結束日期。

> [!NOTE] 
> 資源管理員可以更新所有具有指定結束日期的指派。 此外，成員可以將自助服務要求初始化，以[擴充或更新指派](pim-resource-roles-renew-extend.md)。


## <a name="assignment-states"></a>指派狀態

適用於 Azure 資源的 PIM 有兩個不同的指派狀態，這兩個狀態會顯示在 PIM 的 [我的角色]、[角色] 和 [成員] 檢視中的 [有效角色] 索引標籤上。 這些狀態包括：

- 已指派
- 已啟用

檢視 [有效角色] 中所列的成員資格時，您可以使用 [狀態] 資料行中的值區分「已指派」為有效的使用者與「已啟用」合格指派且現在有效的使用者。

## <a name="next-steps"></a>後續步驟

[指派 Privileged Identity Management 中的角色](pim-resource-roles-assign-roles.md)
