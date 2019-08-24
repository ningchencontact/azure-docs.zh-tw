---
title: 以資料科學虛擬機器為基礎的團隊環境簡介 - Azure | Microsoft Docs
description: 在企業團隊環境中部署「資料科學 VM」的模式。
keywords: 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 小組資料科學流程
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 5f34498fbdacf7fc6e62788913c795ab70ceef23
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991620"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>以資料科學虛擬機器為基礎的團隊分析和 AI 環境 
[資料科學虛擬機器](overview.md)(DSVM) 在 Azure 平臺上提供豐富的環境, 其中包含用於人工智慧 (AI) 和資料分析的預先建立軟體。

傳統上，DSVM 已用來作為個別的分析電腦。 個別資料科學家透過此共用、預先建立的分析環境來獲得生產力。 當大型分析團隊為其資料科學家和 AI 開發人員規劃環境時, 其中一個重複的主題是用於開發和測試的共用分析基礎結構。 此基礎結構是與企業 IT 原則一併管理, 這也有助於跨資料科學和分析團隊進行共同作業和一致性。

共用基礎結構可讓您更有效地流量分析環境。 有些組織會呼叫以小組為基礎的資料科學/分析基礎結構來*分析沙箱*。 它可讓資料科學家存取各種資料資產, 以快速瞭解資料。 此沙箱環境也可協助資料科學家執行實驗、驗證假設, 以及建立預測模型, 而不會影響生產環境。

由於 DSVM 在 Azure 基礎結構層級運作，因此 IT 系統管理員立即就可以將 DSVM 的運作方式設定成符合企業的 IT 原則。 DSVM 提供完整的彈性來執行各種共用架構, 同時也能以控制的方式提供公司資料資產的存取權。

本節將討論可用來將 DSVM 部署成團隊型資料科學基礎結構的一些模式與指導方針。 因為這些模式的構成要素來自于 Azure 基礎結構即服務 (IaaS), 所以會套用至任何 Azure Vm。 這一系列的文章著重于將這些標準 Azure 基礎結構功能套用至 DSVM。

企業小組分析環境的主要建立組塊包括:

* [Dsvm 的自動縮放集區](dsvm-pools.md)
* [從集區中的任何 DSVM 使用通用身分識別並存取工作區](dsvm-common-identity.md)
* [保護對資料來源的存取](dsvm-secure-access-keys.md)


此系列提供上述每個主題的指引和指標。 它不會涵蓋在大型企業設定中部署 Dsvm 的所有考慮和需求。 以下是您在企業中執行 DSVM 實例時可以使用的一些其他 Azure 資源:

* [網路安全性](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [監視](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)和[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [記錄與稽核](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [原則設定和強制執行](../../governance/policy/overview.md)
* [反惡意程式碼](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [加密](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [資料探索和控管](https://docs.microsoft.com/azure/data-catalog/)

最後, [Azure 架構中心](https://docs.microsoft.com/azure/architecture/)提供建立和管理雲端式分析基礎結構的詳細端對端架構和模型。
