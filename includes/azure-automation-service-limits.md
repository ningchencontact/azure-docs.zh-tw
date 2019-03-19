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
ms.openlocfilehash: 61f82771e53ac9bb594484b29bb109a03cee674b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553468"
---
#### <a name="process-automation"></a>程序自動化

| 資源 | 上限 |注意|
| --- | --- |---|
| 新的作業可以提交每個 Azure 自動化帳戶 （未排程的作業） 每隔 30 秒的最大數目 |100 |當到達這個限制時，建立作業，後續要求將會失敗。 用戶端會收到錯誤回應。|
| 並行執行的作業，在相同的執行個體的每個自動化帳戶 （未排程的作業） 的時間的最大數目 |200 |當到達這個限制時，建立作業，後續要求將會失敗。 用戶端會收到錯誤回應。|
| 儲存體大小上限的 30 天的循環內的作業中繼資料 | 10 GB （約 4 百萬個作業）|當到達這個限制時，建立作業，後續要求將會失敗。 |
| 模組可匯入每個自動化帳戶每隔 30 秒的最大數目 |5 ||
| 模組的最大大小 |100 MB ||
| 作業執行時間，免費層 |每個訂用帳戶每行事曆月份 500 分鐘 ||
| 每個沙箱所允許的磁碟空間的最大數量<sup>1</sup> |1 GB |適用於 Azure 的沙箱。|
| 提供給沙箱的記憶體數量上限<sup>1</sup> |400 MB |適用於 Azure 的沙箱。|
| 每個沙箱所允許的網路通訊端數目上限<sup>1</sup> |1,000 |適用於 Azure 的沙箱。|
| 每個 runbook 所允許的最大執行階段<sup>1</sup> |3 小時 |適用於 Azure 的沙箱。|
| 訂用帳戶中的自動化帳戶的最大數目 |沒有限制 ||
|可以在單一的混合式 Runbook 背景工作角色執行的並行作業數目上限|50 ||
| 最大的 runbook 作業參數的大小   | 512 kb||
| 最大的 runbook 參數   | 50|如果達到 50 參數限制，您可以將 JSON 或 XML 字串傳遞至參數，並剖析與 runbook。|
| 最大的 webhook 承載大小 |  512 kb|
| 作業資料會保留的天數上限|30 天|
| PowerShell 工作流程狀態大小上限 |5 MB| 適用於 PowerShell 工作流程 runbook 時檢查點檢查工作流程。|

<sup>1</sup>沙箱是一種共用的環境，可供多個工作。 使用相同的沙箱的工作都受限於沙箱的資源限制。

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
