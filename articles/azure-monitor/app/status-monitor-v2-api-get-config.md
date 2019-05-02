---
title: Azure 狀態監視器 v2 API 參考：取得設定 |Microsoft Docs
description: 狀態監視器 v2 API 參考 Get-ApplicationInsightsMonitoringConfig。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 0e6b47c9b629aed28fa217cb6299edb57423fc6f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870473"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>狀態監視器 v2 API:Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

本文件說明 cmdlet 所隨附的成員身分[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>描述

取得組態檔，並列印到主控台的值。

> [!IMPORTANT] 
> 此 cmdlet 需要系統管理員權限的 PowerShell 工作階段。

## <a name="examples"></a>範例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>參數 

（沒有所需的參數）

## <a name="output"></a>輸出


#### <a name="example-output-from-reading-the-config-file"></a>讀取組態檔的範例輸出

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```
