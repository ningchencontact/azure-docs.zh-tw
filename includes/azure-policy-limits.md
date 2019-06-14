---
title: 包含檔案
description: 包含檔案
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133869"
---
Azure 原則的每個物件類型都有最大計數。 _範圍_的項目表示訂用帳戶或[管理群組](../articles/governance/management-groups/overview.md)。

| Where | 何事 | 最大計數 |
|---|---|---|
| 影響範圍 | 原則定義 | 500 |
| 影響範圍 | 計畫定義 | 100 |
| 租用戶 | 計畫定義 | 1,000 |
| 影響範圍 | 原則或方案指派 | 100 |
| 原則定義 | 參數 | 20 |
| 計畫定義 | 原則 | 100 |
| 計畫定義 | 參數 | 100 |
| 原則或方案指派 | 排除項目 (notScopes) | 400 |
| 原則規則 | 巢狀的條件 | 512 |
