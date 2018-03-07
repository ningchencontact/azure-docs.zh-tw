---
title: "適用於 Azure Stack 的支援客體作業系統 | Microsoft Docs"
description: "可在 Azure Stack 上使用這些客體作業系統。"
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 3eceb740b8115d2eaca517017f6158744d6e8e58
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack 上的支援客體作業系統

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

## <a name="windows"></a>Windows
Azure Stack 支援下表中列出的 Windows 客體作業系統：Marketplace 中的映像可供下載至 Azure Stack。 Marketplace 中沒有可用的 Windows 用戶端映像。

在部署期間，Azure Stack 會將適當版本的客體代理程式插入映像中。

| 作業系統 | 說明 | 發行者 | OS 類型 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 SP1 | 64 位元 | Microsoft | Windows | 資料中心 |
| Windows Server 2012 | 64 位元 | Microsoft | Windows | 資料中心 |
| Windows Server 2012 R2 | 64 位元 | Microsoft | Windows | 資料中心 |
| Windows Server 2016 | 64 位元 | Microsoft | Windows | Datacenter、Datacenter Core、包含容器的 Datacenter |
| Windows 10 (請參閱附註 1) | 64 位元、Pro 和 Enterprise | Microsoft | Windows | 否 |

附註 1：*若要在 Azure Stack 上部署 Windows 10 用戶端作業系統，您必須具備 [Windows 的每位使用者授權](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx)，或者透過合格多租用戶主機服務提供者 ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)) 購買。*


## <a name="linux"></a>Linux

此處所列的 Linux 發佈包含必要的 Windows Azure Linux 代理程式 (WALA)。

> [!NOTE]   
> 不支援使用 WALA 2.2.3 以下版本建置的映像，且不太可能進行部署。 部分 WALA 代理程式版本已知無法在 Azure Stack VM 上運作，包括 2.2.12 版和 2.2.13 版。
>
> [cloud-init](https://cloud-init.io/) 僅適用於 Azure Stack 上的 Ubuntu 發佈。

| 配送映像 | 說明 | 發行者 | Marketplace |
| --- | --- | --- | --- | --- | --- |
| 容器 Linux |  64 位元 | CoreOS | Stable |
| CentOS 型 6.9 | 64 位元 | Rogue Wave | yes |
| CentOS 型 7.4 | 64 位元 | Rogue Wave | yes |
| Debian 8 "Jessie" | 64 位元 | credativ |  yes |
| Debian 9 "Stretch" | 64 位元 | credativ | yes |
| Red Hat Enterprise Linux 7.x (擱置中) | 64 位元 | Red Hat | 否 |
| SLES 11SP4 | 64 位元 | SUSE | yes |
| SLES 12SP3 | 64 位元 | SUSE | yes |
| Ubuntu 14.04-LTS | 64 位元 | Canonical | yes |
| Ubuntu 16.04-LTS | 64 位元 | Canonical | yes |

未來可能支援其他 Linux 發佈。
