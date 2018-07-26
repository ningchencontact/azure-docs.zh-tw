---
title: 包含檔案
description: 包含檔案
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765533"
---
| Name | 商業 URL | 政府 URL | 說明 |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | 用於使用 AAD 的存取控制和身分識別管理 |
| Backup  | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | 用於複寫資料轉送和協調 |
| 複寫 | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | 用於複寫管理作業和協調 |
| 儲存體 | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | 用於存取儲存體帳戶來儲存複寫的資料 |
| 遙測 (選用) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | 用於遙測 |

``time.nist.gov`` 和 ``time.windows.com``：用於檢查所有部署中系統時間與通用時間之間的時間同步處理。


