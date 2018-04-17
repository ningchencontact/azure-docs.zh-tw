---
title: Azure 管理 - 監視 | Microsoft Docs
description: Azure 具備多項服務和工具，可共同運作以完整管理在 Azure 中執行的應用程式，也能顧及其他雲端和內部部署中執行的應用程式。  本文提供不同管理範圍的高階說明，以及 Azure 工具內容的連結，讓您能夠管理雲端應用程式和資源。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: bwren
ms.openlocfilehash: 36dd04be167d9e8e63ab38e4af80c5766ba55370
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="azure-management---monitoring"></a>Azure 管理 - 監視

在 Azure 中監視是 Azure 管理的其中一個層面。  本文簡要說明部署和維護 Azure 中應用程式和資源所需的各種管理範圍，並提供快速入門文件的連結。

## <a name="management-in-azure"></a>在 Azure 中管理

管理是指維護商務應用程式與支援商務應用程式的資源所需的工作和流程。  Azure 具備多項服務和工具，可共同運作以完整管理在 Azure 中執行的應用程式，也能顧及其他雲端和內部部署中執行的應用程式。  若要設計完整的管理環境，首先必須了解各種可用的工具，以及如何依不同的管理案例搭配使用各項工具。

下圖說明維護應用程式或資源所需的不同管理範圍。  您可將這些不同的範圍想像成生命週期，而在資源存留期間將需要這些持續連接的生命週期。  生命週期會從初始部署開始，經過持續的作業，最終遭到淘汰。

![管理功能](media/management-overview/management-capabilities.png)


沒有單一的 Azure 服務能完全符合特定管理範圍的需求，但搭配多項服務就能做到這點。  某些服務會提供具有針對性的功能，像是可用來監視 Web 應用程式的 Application Insights。  其他服務則會提供類似 Log Analytics 的通用功能，可以為其他服務儲存管理資料，讓您分析由不同服務所收集的不同類型資料。  

下列各節簡短描述不同的管理範圍，並針對處理管理範圍，提供主要 Azure 服務詳細內容的連結。

## <a name="monitor"></a>監視
監視係指收集和分析資料來判斷您商務應用程式及其相依資源之效能、健康情況和可用性的行為。 有效的監視策略將可協助您了解應用程式不同元件的詳細作業，並主動通知您重大問題，讓您能夠在這些問題變成問題之前便將其解決，以提升正常運作時間。  [監視 Azure 應用程式和資源](monitoring-overview.md)提供了 Azure 中監視的概觀，可識別監視策略所使用的各種服務。


## <a name="configure"></a>設定
設定是指應用程式及資源的初始部署和設定，以及透過修補程式和更新所持續進行的維護。  您可以透過指令碼和原則將這些工作自動化，以免於重複進行各項工作、將投注的時間及精力降至最低，並增加正確性和效率。  [Azure 自動化](..\automation\automation-intro.md)提供自動化設定工作的主要服務。  它除了讓流程自動化的 Runbook，還提供設定和更新管理，可協助您透過原則管理設定，以及協助識別和部署更新。

## <a name="govern"></a>治理
治理提供多項機制和流程，以便維持控制 Azure 中的應用程式與資源。  它同時也涉及規劃您的新措施，以及設定策略層級的優先順序。  在 Azure 中的治理主要是透過兩個服務進行實作。  [Azure 原則](../azure-policy/azure-policy-introduction.md)可讓您建立、指派和管理會對資源強制執行不同規則和動作的原則定義，讓這些資源能符合公司標準和服務等級協定的規範。 針對您的 Azure 資源及包含 AWS 和 Google 在內的其他雲端服務提供者，[Cloudyn 的 Azure 成本管理](../cost-management/overview.md)可讓您追蹤雲端使用量和費用。

## <a name="secure"></a>安全
管理應用程式、資源和資料的安全性涉及多種動作的組合，其中包括評估威脅、收集和分析安全性資料，以及確保您的應用程式和資源是以安全方式來設計及設定的。  安全性監視和威脅分析是由 [Azure 資訊安全中心](../security-center/security-center-intro.md)所提供，其中包含跨混合式雲端工作負載的整合式安全性管理和進階威脅保護。  另請參閱 [Azure 安全性簡介](../security/azure-security.md)，了解 Azure 中更完整的安全性資訊，以及安全地設定 Azure 資源的指導方針。


## <a name="protect"></a>Protect
保護是指即使發生了您無法控制的中斷意外，也能確保您的應用程式和資料永遠都可以使用。  Azure 中的保護是由兩個服務提供。  [Azure 備份](../backup/backup-introduction-to-azure-backup.md)提供雲端或內部部署中資料的備份和復原。    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 可在災害發生時提供商務持續性與立即復原，以確保應用程式的高可用性。

## <a name="migrate"></a>移轉 
移轉是指將目前正在內部部署環境中執行的工作負載轉換至 Azure 雲端。  [Azure Migrate](../migrate/migrate-overview.md) 是一項服務，可協助您評估移轉適用性，包括將內部部署虛擬機器移轉至 Azure 時，以效能為基礎的規模調整和成本預估。  Azure Site Recovery 可協助您[從內部部署](../site-recovery/migrate-tutorial-on-premises-azure.md)或[從 Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md) 實際移轉虛擬機器。  [Azure 資料庫移轉](../dms/dms-overview.md)會協助您將多個資料庫來源移轉至 Azure 資料平台。


## <a name="operations-management-suite"></a>Operations Management Suite
與 Azure 管理相關的舊技術文件包含 Operations Management Suite (OMS)，隨附於下列 Azure 管理服務：

- Azure 自動化
- Azure 備份
- Log Analytics
- Site Recovery

由於 Azure 的管理項目已完整延伸並包含其他服務，因此我們不會在技術文件中說明此隨附項目。 任何屬於 OMS 的服務均不會變更，而每項服務仍然在管理 Azure 應用程式和資源上扮演重要的角色。 您應該把焦點放在需要執行的管理工作，以及每項工作需有不同的 Azure 服務共同合作這件事上。