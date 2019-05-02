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
ms.openlocfilehash: 04929de241ff36e4d67f24907747d89a16a73898
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733729"
---
#### <a name="process-automation"></a>程序自動化

| Resource | 上限 |注意|
| --- | --- |---|
| 每个 Azure 自动化帐户每 30 秒可以提交的新作业的最大数量（非计划的作业） |100 |达到此限制时，后续作业创建请求会失败。 用戶端會收到錯誤回應。|
| 每个自动化帐户相同时间实例并发运行的作业的最大数量（非计划的作业） |200 |达到此限制时，后续作业创建请求会失败。 用戶端會收到錯誤回應。|
| 30 天滚动期内作业元数据的最大存储大小 | 10 GB（约 400 万个作业）|达到此限制时，后续作业创建请求会失败。 |
| 最大的工作資料流限制|1MB|單一資料流不能大於 1 MB。|
| 每个自动化帐户每 30 秒可以导入的模块的最大数量 |5 ||
| 模块的最大大小 |100 MB ||
| 作业运行时间，免费层 |每個訂用帳戶每行事曆月份 500 分鐘 ||
| 每个沙盒允许的最大磁盘空间<sup>1</sup> |1 GB |仅适用于 Azure 沙盒。|
| 沙盒的最大内存量<sup>1</sup> |400 MB |仅适用于 Azure 沙盒。|
| 每个沙盒允许的最大网络套接字数量<sup>1</sup> |1,000 |仅适用于 Azure 沙盒。|
| 每个 runbook 允许的最大运行时<sup>1</sup> |3 小時 |仅适用于 Azure 沙盒。|
| 订阅中自动化帐户的最大数目 |沒有限制 ||
| 混合式背景工作角色群組，每個自動化帳戶的最大數目|4,000||
|可以在單一的混合式 Runbook 背景工作角色執行的並行作業數目上限|50 ||
| Runbook 作业参数大小上限   | 512 千比特||
| Runbook 参数数量上限   | 50|如果达到 50 个参数的限制，则可将 JSON 或 XML 字符串传递给参数，并使用 Runbook 对其进行分析。|
| Webhook 有效负载大小上限 |  512 千比特|
| 保留作业数据的最大天数|30 天|
| PowerShell 工作流状态大小上限 |5 MB| 执行检查点工作流时适用于 PowerShell 工作流 runbook。|

<sup>1</sup>沙盒是可以由多个作业使用的共享环境。 使用同一沙盒的作业受沙盒的资源限制约束。

#### <a name="change-tracking-and-inventory"></a>變更追蹤和清查

下表顯示每一機器的變更追蹤的追蹤項目限制。

| **Resource** | **限制**| **注意事項** |
|---|---|---|
|檔案|500||
|登錄|250||
|Windows 軟體|250|不包含軟體更新。|
|Linux 套件|1,250||
|服務|250||
|精靈|250||
