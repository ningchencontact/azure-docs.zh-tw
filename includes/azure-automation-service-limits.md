---
title: 包含檔案
description: 包含檔案
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: f3ae2289112948dea7d2649c4fad6b1cafb3804b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444023"
---
#### <a name="process-automation"></a>程序自動化

| 資源 | 上限 |注意|
| --- | --- |---|
| 每個自動化帳戶每隔 30 秒可以送出的新作業數量上限 (非排程作業) |100 |達到此限制時，後續的建立作業要求會失敗。 用戶端會收到錯誤回應。|
| 每個自動化帳戶在相同時間點的並行執行作業數量上限 (非排程作業) |200 |達到此限制時，後續的建立作業要求會失敗。 用戶端會收到錯誤回應。|
| 30 天累積期間內作業中繼資料的儲存體大小上限。 | 10 GB (約 4 百萬個作業)|達到此限制時，後續的建立作業要求會失敗。 |
| 每個自動化帳戶每隔 30 秒可匯入的模組數量上限 |5 ||
| 模組的大小上限 |100 MB ||
| 工作執行時間 - 免費層 |每個訂用帳戶每行事曆月份 500 分鐘 ||
| 每個沙箱所允許的磁碟空間最大數量 **<sup>1</sup>** |1 GB |僅適用於 Azure 沙箱|
| 提供給沙箱的記憶體大小上限 **<sup>1</sup>** |400 MB |僅適用於 Azure 沙箱|
| 每個沙箱所允許的網路通訊端數目上限 **<sup>1</sup>** |1000 |僅適用於 Azure 沙箱|
| 每個 Runbook 所允許的執行階段上限 **<sup>1</sup>** |3 小時 |僅適用於 Azure 沙箱|
| 訂用帳戶中自動化帳戶的最大數目 |沒有限制 ||
|在單一混合式 Runbook 背景工作角色上執行的並行作業最大數目|50 ||
| Runbook 作業參數大小上限   | 512 kb||
| Runbook 參數上限   | 50|您可以將 JSON 或 XML 字串傳遞至參數，並，如果達到 50 個參數限制，則使用 Runbook 進行剖析|
| Webhook 裝載大小上限 |  512 kb|
| 保留作業資料的最大天數|30 天|
| PowerShell 工作流程狀態的大小上限 |5 MB| 適用於檢查點檢查工作流程時的 PowerShell 工作流程 Runbook。|

**<sup>1</sup>** 沙箱是共用的環境，可以讓多個作業使用，使用相同沙箱的作業受限於沙箱的資源限制。

#### <a name="change-tracking-and-inventory"></a>變更追蹤與詳細目錄

下表顯示對於變更追蹤所追蹤的每個機器項目限制。

| **Resource** | **限制**| **注意事項** |
|---|---|---|
|檔案|500||
|登錄|250||
|Windows 軟體|250|不包含軟體更新|
|Linux 套件|1250||
|服務|250||
|精靈|250||
