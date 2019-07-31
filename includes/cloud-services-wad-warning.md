---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "67174321"
---
> [!WARNING]
> 當您在現有角色上啟用診斷功能時，您已設定的任何擴充功能都會在部署套件時停用。 它們包括：
>
> * Microsoft Monitoring Agent 診斷
> * Microsoft Azure 安全性監控
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Microsoft 服務分析工具代理程式      
> * Windows Azure 網域擴充功能        
> * Windows Azure 診斷擴充功能   
> * Windows Azure 遠端桌面擴充功能
> * Windows Azure 記錄檔收集器
>
> 在部署更新的角色之後，您可以透過 Azure 入口網站或 PowerShell 重設您的擴充功能。
>
