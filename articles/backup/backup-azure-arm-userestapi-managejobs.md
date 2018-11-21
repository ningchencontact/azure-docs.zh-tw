---
title: Azure 備份︰使用 REST API 管理備份作業
description: 使用 REST API 管理 Azure 備份的備份和還原作業
services: backup
author: pvrk
manager: shivamg
keywords: REST API; Azure VM 備份; Azure VM 還原;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: eb8b7dc77d180eb56c2585e93e60a36742f6c84c
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289425"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>使用 REST API 追蹤備份和還原

Azure 備份服務會在各種案例中觸發在背景執行的作業，例如觸發備份、還原作業、停用備份。 這些工作可以使用其識別碼進行追蹤。

## <a name="fetch-job-information-from-operations"></a>從作業 (Operation) 擷取作業 (Job) 資訊

觸發備份之類的作業一律會傳回 jobID。 例如：[觸發備份 REST API 作業](backup-azure-arm-userestapi-backupazurevms.md#example-responses-3)的最後回應如下所示：

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Azure VM 備份作業是由 "jobId" 欄位來識別，而且可如[這裡](https://docs.microsoft.com/rest/api/backup/jobdetails/)所述使用簡單的 *GET* 要求進行追蹤。

## <a name="tracking-the-job"></a>追蹤作業

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2017-07-01
```

`{jobName}` 是上面提及的 "jobId"。 回應一律為「200 確定」，其 [狀態] 欄位會指出作業的目前狀態。 一旦為 "Completed" 或 "CompletedWithWarnings"，'extendedInfo' 區段就會顯示更多有關作業的詳細資訊。

### <a name="response"></a>Response

|名稱  |類型  |說明  |
|---------|---------|---------|
|200 確定     | [JobResource](https://docs.microsoft.com/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>範例回應

一旦提交 GET URI，就會傳回 200 (確定) 回應。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```