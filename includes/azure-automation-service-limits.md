---
title: 包含檔案
description: 包含檔案
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852036"
---
| 資源 | 上限 |注意|
| --- | --- |---|
| 每個自動化帳戶每隔 30 秒可以送出的新作業數量上限 (非排程作業) |100 |達到此限制時，後續的建立作業要求會失敗。 用戶端會收到錯誤回應。|
| 每個自動化帳戶在相同時間點的並行執行作業數量上限 (非排程作業) |200 |達到此限制時，後續的建立作業要求會失敗。 用戶端會收到錯誤回應。|
| 每個自動化帳戶每隔 30 秒可匯入的模組數量上限 |5 ||
| 模組的大小上限 |100 MB ||
| 工作執行時間 - 免費層 |每個訂用帳戶每行事曆月份 500 分鐘 ||
| 每個沙箱所允許的磁碟空間最大數量**<sup>1</sup>** |1 GB |僅適用於 Azure 沙箱|
| 提供給沙箱的記憶體大小上限**<sup>1</sup>** |400 MB |僅適用於 Azure 沙箱|
| 每個沙箱所允許的網路通訊端數目上限**<sup>1</sup>** |1000 |僅適用於 Azure 沙箱|
| 訂用帳戶中自動化帳戶的最大數目 |沒有限制 ||
|在單一混合式 Runbook 背景工作角色上執行的並行作業最大數目|50 ||

**<sup>1</sup>** 沙箱是共用的環境，可以讓多個作業使用，使用相同沙箱的作業受限於沙箱的資源限制。
