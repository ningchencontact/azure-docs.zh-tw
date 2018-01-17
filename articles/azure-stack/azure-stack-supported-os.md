---
title: "適用於 Azure Stack 的支援客體作業系統 | Microsoft Docs"
description: "可在 Azure Stack 上使用這些客體作業系統。"
services: azure-stack
documentationcenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: JeffGoldner
ms.openlocfilehash: 0a31da6cbc2c245b959825a4e715d0dc7511ba99
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack 上的支援客體作業系統

*適用於：Azure Stack 整合系統和 Azure Stack 開發封裝*

## <a name="windows"></a>Windows
Azure Stack 支援下列 Windows 客體作業系統。 Marketplace 中的映像可供下載至 Azure Stack。 Marketplace 中沒有可用的 Windows 用戶端映像。

在部署期間，Azure Stack 會確保插入映像中的客體代理程式為適當版本。

| 作業系統 | 說明 | 發行者 | OS 類型 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 位元 | Microsoft | Windows | 資料中心 |
| Windows Server 2012 | 64 位元 | Microsoft | Windows | 資料中心 |
| Windows Server 2012 R2 | 64 位元 | Microsoft | Windows | 資料中心 |
| Windows Server 2016 | 64 位元 | Microsoft | Windows | Datacenter、Datacenter Core、包含容器的 Datacenter |
| Windows 7 | 64 位元，Pro 和 Enterprise | Microsoft | Windows | 否 |
| Windows 8.1 | 64 位元，Pro 和 Enterprise | Microsoft | Windows | 否 |
| Windows 10 | 64 位元，Pro 和 Enterprise | Microsoft | Windows | 否 |

## <a name="linux"></a>Linux

此處所列的 Linux 發佈包含必要的 Windows Azure Linux 代理程式 (WALA)。 

> [!NOTE]
> 不支援使用 WALA 2.2.3 以下版本建置的映像，且不太可能進行部署。

| 配送映像 | 說明 | 發行者 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| 容器 Linux |  64 位元 | CoreOS | Stable |
| CentOS 型 6.9 | 64 位元 | Rogue Wave | yes |
| CentOS 型 7.3 | 64 位元 | Rogue Wave | yes |
| CentOS 型 7.4 | 64 位元 | Rogue Wave | yes |
| Debian 8 "Jessie" | 64 位元 | credativ |  yes |
| Debian 9 "Stretch" | 64 位元 | credativ | yes |
| Oracle Linux | 64 位元 | Oracle | 否 |
| Red Hat Enterprise Linux 7.x | 64 位元 | Red Hat | 否 |
| SLES 11SP4 | 64 位元 | SUSE | yes |
| SLES 12SP3 | 64 位元 | SUSE | yes |
| Ubuntu 14.04-LTS | 64 位元 | Canonical | yes |
| Ubuntu 16.04-LTS | 64 位元 | Canonical | yes |

未來可能支援其他 Linux 發佈。




