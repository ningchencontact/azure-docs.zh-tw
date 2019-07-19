---
title: Azure 狀態監視器 v2 API 參考:停用檢測引擎 |Microsoft Docs
description: 狀態監視器 v2 API 參考。 停用-InstrumentationEngine。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
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
ms.openlocfilehash: 827f8555718a22979617c9b0794dce3bd92207f4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326398"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine"></a>狀態監視器 v2 API:停用-InstrumentationEngine

本文說明的 Cmdlet 是[ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述
藉由移除一些登錄機碼來停用檢測引擎。
重新開機 IIS, 變更才會生效。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話。

## <a name="examples"></a>範例

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>參數 

### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來輸出詳細記錄。

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>成功停用檢測引擎的範例輸出

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>後續步驟

 使用狀態監視器 v2 來執行更多動作:
 - 使用我們的指南進行狀態監視器 v2 的[疑難排解](status-monitor-v2-troubleshoot.md)。
