---
title: "Azure 中 Linux VM 的已排定事件 | Microsoft Docs"
description: "使用您的 Linux 虛擬機器的 Azure 中繼資料服務中排程事件。"
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: ae9955253647f3277729e7905baf7bb07645de42
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2018
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure 的中繼資料服務： 排程的事件 （預覽） 適用於 Linux Vm

> [!NOTE] 
> 若您同意使用規定即可取得預覽。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

排程的事件是在 Azure 中繼資料服務，可讓您的應用程式時間準備虛擬機器 (VM) 的維護工作的子服務。 它提供即將進行維護事件的相關資訊 （例如，在重新開機），讓您的應用程式可以準備，並限制中斷。 所有的 Azure 虛擬機器型別，包括 PaaS 和 IaaS Windows 和 Linux 上的使用。 

如需 Windows 上已排定事件的資訊，請參閱 [Windows VM 的已排定事件](../windows/scheduled-events.md)。

## <a name="why-use-scheduled-events"></a>為何要使用排程的事件嗎？

許多應用程式可以從時間準備 VM 維護獲益。 時間可以用來執行應用程式專屬提升可用性、 可靠性與服務性的工作包括： 

- 檢查點和還原。
- 清空連接。
- 主要複本的容錯移轉。
- 從負載平衡器集區移除。
- 事件記錄。
- 正常關機。

排程的事件與您的應用程式可以探索時維護會發生觸發工作，以限制其影響。  

排程的事件會提供下列使用案例中的事件：

- 平台起始維護 （例如，主機 OS 更新）
- 使用者起始維護 （例如，使用者會重新啟動或重新部署 VM）

## <a name="the-basics"></a>基本概念  

  中繼資料服務會公開執行 Vm 使用的 REST 端點可從 VM 內存取的相關資訊。 資訊是透過 nonroutable IP，使它不會公開外部 VM。

### <a name="scope"></a>範圍
排程的事件會傳送到：

- 雲端服務中的所有 Vm。
- 可用性設定組中的所有 Vm。
- 小數位數設定放置群組中的所有 Vm。 

如此一來，檢查`Resources`欄位中的事件來識別哪些 Vm 會受到影響。

### <a name="discover-the-endpoint"></a>探索端點
對於可供虛擬網路的 Vm，排程的事件的最新版本的完整端點是： 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

虛擬網路內建立 VM 的位置的情況下，中繼資料服務可以使用從靜態 nonroutable IP， `169.254.169.254`。
如果 VM 未建立虛擬網路，在預設情況下，雲端服務和傳統的 Vm 內需要額外的邏輯來探索要使用的 IP 位址。 若要了解如何[探索主機端點](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)，請參閱此範例。

### <a name="versioning"></a>版本控制 
已排程的事件服務進行版本設定。 版本是必要項，而目前的版本是`2017-08-01`。

| 版本 | 版本資訊 | 
| - | - | 
| 2017-08-01 | <li> 已從 IaaS VM 的資源名稱中移除預留底線<br><li>強制所有要求的中繼資料標頭需求 | 
| 2017-03-01 | <li>公開預覽版本


> [!NOTE] 
> 舊版的 api 版本 {最新} 支援的排程事件的預覽版本。 此格式將不再受到支援且之後會遭到取代。

### <a name="use-headers"></a>使用標頭檔
當您查詢中繼資料服務時，您必須提供標頭`Metadata:true`以確保沒有不小心重新導向。 所有排程的事件都需要 `Metadata:true` 標頭。 無法在要求中包含的標頭是由中繼資料服務的 「 不正確的要求 」 回應所導致。

### <a name="enable-scheduled-events"></a>啟用排定的事件
您可以提出要求的排程的事件，第一次 Azure 隱含啟用 VM 上的功能。 如此一來，在您第一次呼叫的最長兩分鐘預期回應延遲。

> [!NOTE]
> 如果您的服務不會呼叫一天的結束點排程的事件會自動停用您的服務。 您的服務已停用排程的事件之後，使用者啟動維護不建立任何事件。

### <a name="user-initiated-maintenance"></a>使用者起始的維護
透過 Azure 入口網站、 API、 CLI 或 PowerShell 的使用者啟動 VM 維護導致排程的事件。 您接著可以測試維護準備邏輯應用程式中，您的應用程式就可以準備進行使用者起始的維護。

如果您重新啟動 VM 時，具類型的事件`Reboot`排程。 如果您重新部署 VM 時，具類型的事件`Redeploy`排程。

> [!NOTE] 
> 目前，您可以同時排程最多 100 個使用者起始的維護作業。

> [!NOTE] 
> 目前，不是可設定排程的事件會導致使用者起始的維護。 預計在未來版本中，將可針對此狀況作設定。

## <a name="use-the-api"></a>使用 API

### <a name="query-for-events"></a>查詢事件
您可以藉由下列呼叫查詢排程的事件：

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

回應包含排定的事件陣列。 為空陣列，表示，目前沒有事件排程。
在案例中回應沒有排程的事件，其中包含事件的陣列。 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>事件屬性
|內容  |  說明 |
| - | - |
| EventId | 此事件的全域唯一識別碼。 <br><br> 範例: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 此事件造成的影響。 <br><br> 值: <br><ul><li> `Freeze`: VM 排程暫停幾秒鐘的時間。 暫止 CPU，但是不會影響記憶體、 開啟的檔案或網路連線。 <li>`Reboot`: VM 已排定重新開機。 （非持續的記憶體是遺失）。 <li>`Redeploy`： 排定 VM 移到其他節點。 （暫時磁碟會遺失）。 |
| ResourceType | 此事件影響的資源類型。 <br><br> 值: <ul><li>`VirtualMachine`|
| 資源| 此事件會影響的資源的清單。 保證清單包含從最多一個機器[更新網域](manage-availability.md)，但它可能不會包含 UD 中的所有機器。 <br><br> 範例: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | 此事件的狀態。 <br><br> 值： <ul><li>`Scheduled`︰此事件已排定在 `NotBefore` 屬性所指定的時間之後啟動。<li>`Started`︰已啟動事件。</ul> 否`Completed`或曾經提供類似的狀態。 事件不會再傳回事件完成時。
| NotBefore| 這個事件可以啟動之後的時間。 <br><br> 範例: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>事件排定
每個事件已排程的時間在未來最小數量取決於事件類型。 事件的 `NotBefore` 屬性會反映這個時間。 

|EventType  | 最小的注意事項 |
| - | - |
| 凍結| 15 分鐘 |
| 重新開機 | 15 分鐘 |
| 重新部署 | 10 分鐘 |

### <a name="start-an-event"></a>啟動事件 

您即將發生事件的深入了解並完成您的邏輯非失誤性關機之後，您可以藉由核准未處理的事件`POST`至中繼資料服務呼叫`EventId`。 這個呼叫表示至 Azure，它可以縮短最小通知的時間 （可能的話）。 

下例會 JSON 中必須有`POST`要求本文。 要求需包含 `StartRequests` 清單。 每個`StartRequest`包含`EventId`您想要加速的事件：
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash 範例
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> 用來認可事件可讓繼續的所有事件`Resources`事件中，不只是 VM，認可事件。 因此，您可以選擇選擇前置字元來協調認可中，可能會在第一部機器一樣簡單`Resources`欄位。

## <a name="python-sample"></a>Python 範例 

下列範例會查詢中繼資料服務取得排程的事件，並核准每個未處理的事件：

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>後續步驟 
- 檢閱中的排程的事件的程式碼範例[Azure 執行個體中繼資料已排程的事件 Github 儲存機制](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)。
- 如需詳細資訊中可用的 Api[執行個體中繼資料服務](instance-metadata-service.md)。
- 了解 [Azure 中 Linux 虛擬機器預定進行的維修](planned-maintenance.md)。
