---
title: "疑難排解 Azure 記錄分析 Linux 代理程式 |Microsoft 文件"
description: "記錄分析 Linux 代理程式，說明徵狀、 原因和解決最常見的問題。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2017
ms.author: magoedte
ms.openlocfilehash: 5f598da9b82b4425ca509a26a2e6e366ba4c3394
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>如何疑難排解 Linux 代理程式，以供記錄分析的問題

本文章提供疑難排解的錯誤，您可能會遇到 Linux 代理程式，以供記錄分析，並建議解決問題的可能方案的說明。

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>問題︰ 無法透過 Proxy 連線至 Log Analytics

### <a name="probable-causes"></a>可能的原因
* 上架期間指定的 Proxy 不正確
* Log Analytics 和 Azure 自動化服務端點未列在資料中心的白名單中 

### <a name="resolutions"></a>解決方式
1. 使用下列命令搭配已啟用的 `-v` 選項，以便透過 OMS Agent for Linux 重新上架至 Log Analytics 服務。 這可讓透過 Proxy 連接到 OMS 服務的代理程式產生詳細資訊輸出。 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. 檢閱[設定代理程式以搭配 Proxy 伺服器或 OMS 閘道使用](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway)一節，以確認您已適當地設定代理程式透過 Proxy 伺服器通訊。    
* 再次檢查以確認下列 Log Analytics 服務端點已列在白名單中：

    |代理程式資源| 連接埠 |  
    |------|---------|  
    |*.ods.opinsights.azure.com | 連接埠 443|   
    |*.oms.opinsights.azure.com | 連接埠 443|   
    |ods.systemcenteradvisor.com | 連接埠 443|   
    |*.blob.core.windows.net/ | 連接埠 443|   

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>問題：您在嘗試上架時收到 403 錯誤

### <a name="probable-causes"></a>可能的原因
* Linux 伺服器上的日期與時間不正確 
* 使用的工作區識別碼和工作區金鑰不正確

### <a name="resolution"></a>解決方案

1. 使用命令日期檢查 Linux 伺服器上的時間。 如果時間為自目前時間起的 + /-15 分鐘，則上架失敗。 若要修正此問題，請更新 Linux 伺服器的日期和/或時區。 
2. 確認您已安裝最新版的 OMS Agent for Linux。  最新版本現在會通知您時間差異是否造成上架失敗。
3. 使用正確的工作區識別碼和工作區金鑰並遵循本主題中前面的安裝指示重新上架。

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>問題︰上架後您隨即在記錄檔中看到 500 與 404 錯誤
這是已知第一次將 Linux 資料上傳至 Log Analytics 工作區時會發生的問題。 這不會影響正在傳送的資料或服務體驗。

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>問題︰您在 Azure 入口網站中看不到任何資料

### <a name="probable-causes"></a>可能的原因

- 上架至 Log Analytics 服務失敗
- 對 Log Analytics 服務的連線遭到封鎖
- OMS Agent for Linux 資料已備份

### <a name="resolutions"></a>解決方式
1. 確認 Log Analytics 服務是否成功上架，做法是檢查下列檔案是否存在：`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. 使用 `omsadmin.sh` 命令列指示重新上架
3. 如果使用 Proxy，請參閱稍早所提供的 Proxy 解決步驟。
4. 在某些情況下，當 OMS Agent for Linux 無法與服務通訊時，系統會將整個緩衝區大小 (亦即 50 MB) 的資料加入佇列。 應該執行下列命令重新啟動 OMS Agent for Linux：`/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`。 

    >[!NOTE]
    >此問題已在代理程式 1.1.0-28 版和更新版本中修正。

