---
title: Azure 狀態監視器 v2 API 參考：停用檢測引擎 |Microsoft Docs
description: 狀態監視器 v2 API 參考停用-InstrumentationEngine。 監視網站效能，而不必重新部署網站。 使用裝載於內部部署、VM 中或 Azure 上的 ASP.NET Web 應用程式。
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
ms.openlocfilehash: 7d1b658da59c0e778c82301077ef27bdd9c9e614
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144896"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine-v021-alpha"></a>狀態監視器 v2 API:停用 InstrumentationEngine (v0.2.1-alpha)

本文件說明 cmdlet 所隨附的成員身分[Az.ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 狀態監視器 v2 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱[補充使用條款的 Microsoft Azure 預覽版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>描述

此 cmdlet 會移除某些登錄機碼來停用檢測引擎。
重新啟動 IIS，這些變更才會生效。

> [!IMPORTANT] 
> 此 cmdlet 需要系統管理員權限的 PowerShell 工作階段。

## <a name="examples"></a>範例

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>參數 

### <a name="-verbose"></a>-Verbose
**常見的參數。** 您可以使用這個參數，輸出的詳細的記錄。

## <a name="output"></a>輸出


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>已成功停用檢測引擎的範例輸出

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>後續步驟

 進一步運用狀態監視器 v2:
 - 使用我們的指南，來[疑難排解](status-monitor-v2-troubleshoot.md)狀態監視器 v2。
