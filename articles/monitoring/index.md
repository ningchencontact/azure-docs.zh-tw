---
title: Azure 管理 - 監視 | Microsoft Docs
description: Azure 具備多項服務和工具，可共同運作以完整管理在 Azure 中執行的應用程式，也能顧及其他雲端和內部部署中執行的應用程式。  這篇文章提供不同管理範圍的高階說明，以及 Azure 工具的連結內容，讓您管理雲端應用程式和資源。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: bwren
ms.openlocfilehash: b20283e1189e4f1a3555e2dd8d25972c9a677cd6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="azure-management---monitoring"></a>Azure 管理 - 監視

在 Azure 中監視是 Azure 管理的其中一個層面。  本文提供文件連結，簡要說明部署和維護 Azure 中的應用程式和資源所需的各種管理範圍，讓您快速入門。

## <a name="management-in-azure"></a>在 Azure 中管理

管理是指維護您的商務應用程式所需的工作和流程，以及支援商務應用程式所需的資源。  Azure 具備多項服務和工具，可共同運作以完整管理在 Azure 中執行的應用程式，也能顧及其他雲端和內部部署中執行的應用程式。  若要設計完整的管理環境，首先必須了解各種可用的工具，以及如何依不同的管理案例搭配使用各項工具。

下圖說明維護應用程式或資訊所需的不同管理區域。  您可將這些不同的範圍想像成生命週期，而這些生命週期是永續使用資源不可或缺的要件。  生命週期會在初始部署開始並持續作業，最終遭到淘汰。

![管理功能](media/management-overview/management-capabilities.png)


沒有單一的 Azure 服務能完全符合特定管理範圍的需求，但搭配多項服務就能做到這點。  某些服務會提供類似 Application Insights 的目標式功能，可用來監視 Web 應用程式。  其他服務則會提供類似 Log Analytics 的通用功能以儲存其他服務的管理資料，讓您分析由不同服務所收集的不同資料類型。  

下列各節簡短描述不同的管理範圍，並提供主要 Azure 服務詳細內容的連結。

## <a name="monitor"></a>監視
監視係指收集和分析資料來判斷您商務應用程式及其相依資源之效能、健康情況和可用性的行為。 有效的監視策略將可協助您了解應用程式不同元件的詳細作業，並主動通知您重大問題，讓您能夠在這些問題變成問題之前便將其解決，以提升正常運作時間。  您可以參閱[監視 Azure 應用程式和資源](monitoring-overview.md)，了解在 Azure 中監視的概觀，以及監視策略所使用的各種服務。


## <a name="configure"></a>設定
設定是指初始部署和應用程式及資源的組態，以及透過修補程式和更新進行的維護。  透過指令碼和原則將這些工作自動化，可讓您免於重複進行各項工作、將投注的時間及精力降至最低，並增加精確度和效率。  [Azure 自動化](..\automation\automation-intro.md)提供大量的服務供您將各項組態工作自動化。  除了讓程序自動執行的 Runbook，它還提供組態和更新管理，協助您透過原則管理組態，並識別和部署更新。

## <a name="govern"></a>治理
治理可提供多項機制和流程，讓您持續控制應用程式與 Azure 中的資源。  同時也包括規劃您的方案，以及設定策略層級的優先順序。  在 Azure 中的治理主要是透過兩個服務進行實作。  [Azure 原則](../azure-policy/azure-policy-introduction.md)可讓您建立、指派和管理會對資源強制執行不同規則和動作的原則定義，讓這些資源能符合公司標準和服務等級協定的規範。 針對您的 Azure 資源及包含 AWS 和 Google 在內的其他雲端服務提供者，[Cloudyn 的 Azure 成本管理](../cost-management/overview.md)可讓您追蹤雲端使用量和費用。

## <a name="secure"></a>安全
管理應用程式、資源和資料的安全性，包括評估威脅、收集和分析安全性資料的組合，並確保您的應用程式和資源是依安全性而加以設計及設定的。  安全性監視和威脅分析由 [Azure 資訊安全中心](../security-center/security-center-intro.md)提供，包含跨混合式雲端工作負載的統一安全性管理和進階的威脅保護。  另請參閱 [Azure 安全性簡介](../security/azure-security.md)，了解 Azure 中更完整的安全性資訊，以及安全地設定 Azure 資源的指導方針。


## <a name="protect"></a>保護
保護指的是即使發生了您無法控制的服務中斷意外，也能確保您的應用程式和資料永遠都可以使用。  Azure 中的保護是由兩個服務提供。  [Azure 備份](../backup/backup-introduction-to-azure-backup.md)提供雲端或內部部署中資料的備份和復原服務。    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 藉由提供業務續航力以及災害發生時立即復原的服務，確保應用程式的高可用性。

## <a name="migrate"></a>移轉 
移轉是指轉換目前正在執行內部部署至 Azure 雲端的工作負載。  [Azure Migrate](../migrate/migrate-overview.md) 是一項服務，可協助您評估移轉適用性，包括將虛擬機器內部部署至 Azure 時以效能為基礎來調整大小和成本預估。  Azure Site Recovery 可協助您[從內部部署](../site-recovery/migrate-tutorial-on-premises-azure.md)或[從 Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md) 實際移轉虛擬機器。  [Azure 資料庫移轉](../dms/dms-overview.md)會協助您將多個資料庫來源遷移至 Azure 資料平台。