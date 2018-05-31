---
title: 以資料科學虛擬機器為基礎的團隊環境簡介 - Azure | Microsoft Docs
description: 將資料科學 VM 部署為企業團隊環境的模式。
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process, 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 團隊資料科學流程
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361366"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>以資料科學虛擬機器為基礎的團隊分析和 AI 環境 
[資料科學虛擬機器](overview.md) (DSVM) 在 Azure 雲端上提供豐富的環境，其中包含了適用於 AI 和資料分析的預先建立軟體。 傳統上，DSVM 都是用來當作個別的分析電腦，而個別的資料科學家可以藉由周知的預先建立分析環境來提升生產力。 當大型分析團隊在規劃其資料科學家和 AI 開發人員的分析環境時，其中一個重複出現的主題便是管理方式與企業 IT 原則一致的共用分析開發與實驗基礎結構，同時也要能輔助跨整個資料科學 / 分析團隊的共同作業和一致性。 共用的基礎結構也可讓 IT 能夠更充分地利用分析環境。 某些組織也將團隊資料科學 / 分析基礎結構稱為「分析沙箱」。分析沙箱可讓資料科學家以安全的方式快速了解資料、執行實驗、驗證假設、建立預測模型，而不會在存取各種資料資產時影響生產環境。 

由於 DSVM 在 Azure 基礎結構層級運作，所以 IT 系統管理員可以方便地將 DSVM 的運作設定成符合企業 IT 原則，並能提供極大的彈性以實作各種共用基礎結構，以便在受控的方式下存取公司資料資產。 

本節將討論可用來將 DSVM 部署成團隊資料科學基礎結構的某些模式與方針。  這些模式的建構元素直接取自 Azure IaaS (基礎結構即服務)，因此可適用於任何 Azure VM。 此系列文章主要著重在將這些標準 Azure 基礎結構功能應用到資料科學 VM。 

企業團隊分析環境的部分重要建構元素是：

* [資料科學虛擬機器的自動調整規模集區](dsvm-pools.md)
* [對於集區中任何 DSVM 工作區的一般身分識別與存取](dsvm-common-identity.md)
* [安全存取資料來源](dsvm-secure-access-keys.md)


此系列文章將提供上述各層面的指導方針和建議資訊。 很明顯地，在大型企業組態中部署 DSVM 還有許多其他考量和需求，而這些並未直接涵蓋在這一系列的文章中。 以下是一般 Azure 文件的一些其他考量與指標，當您在企業中的 DSVM 執行個體上進行實作時可輕易地使用這些資訊。 

* [網路安全性](https://docs.microsoft.com/azure/security/azure-network-security)
* [監視](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)與[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [記錄與稽核](https://docs.microsoft.com/azure/security/azure-log-audit)
* [角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [原則設定和強制執行](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [反惡意程式碼](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [加密](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [資料探索和控管](https://docs.microsoft.com/azure/data-catalog/)

[Azure 架構中心](https://docs.microsoft.com/en-us/azure/architecture/)也是絕佳資源，可提供用於建立和管理雲端式分析基礎結構的詳細端對端架構和模式。 
