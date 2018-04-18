---
title: 如何在適用於 Azure 資源的 Privileged Identity Management 中執行存取權檢閱 | Microsoft Docs
description: 本文件說明如何在適用於 Azure 資源的 PIM 中執行存取權檢閱。
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
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: f1bcf114b997c3056016b84cafc28253ea1af28e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="resource-dashboards"></a>資源儀表板

「系統管理員檢視」儀表板有四個主要元件。 展示過去七天資源角色啟用的圖表。 此資料範圍限制至所選資源，並會顯示最常見角色的啟用 (擁有者、參與者和使用者存取系統管理員)，也會將所有角色的啟用結合顯示。

啟用圖形的右邊為兩個圖表，顯示依指派類型分類的角色指派分佈，類型為使用者和群組。 選取圖表的一部分可將值更改為特定百分比 (反之亦然)。

![](media/azure-pim-resource-rbac/rbac-overview-top.png)

圖表下方會顯示過去 30 天中獲指派新角色的使用者和群組數 (左)，以及以總計指派數排序的角色清單 (遞減)。

![](media/azure-pim-resource-rbac/role-settings.png)
