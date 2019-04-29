---
title: 在 Azure 資訊安全中心內保護 App Service | Microsoft Docs
description: 本文可協助您開始在「Azure 資訊安全中心」內保護 App Service。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: monhaber
ms.openlocfilehash: ea738535ae9326109a7c3fdd0b5d0c4f4691fdf0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095263"
---
# <a name="protect-app-service-with-azure-security-center"></a>使用 Azure 資訊安全中心來保護 App Service
本文可協助您使用「Azure 資訊安全中心」來監視及保護在 App Service 上執行的應用程式。

App Service 可讓您以所選程式設計語言來建置及裝載 Web 應用程式，而無需管理基礎結構。 App Service 提供自動調整功能和高可用性、同時支援 Windows 和 Linux，並可從 GitHub、Azure DevOps 或任何 Git 存放庫進行自動化部署。 

Web 應用程式中的弱點經常會遭到攻擊者利用，因為對網際網路上的幾乎所有組織來說，它們都具有一個通用且動態的介面。 對在 App Service 上執行之應用程式所發出要求會經過部署在全球各地 Azure 資料中心內的數個閘道，這些閘道會負責將每個要求路由傳送至其相對應的應用程式。 

「Azure 資訊安全中心」可針對在 App Service 中執行的應用程式，在 VM 或內部部署執行個體的沙箱中執行評量和建議。 藉由利用 Azure 作為雲端提供者所具備的可見性，「資訊安全中心」將可分析您的 App Service 內部記錄，以監視是否有通常會跨數個目標執行的常見 Web 應用程式攻擊。

「資訊安全中心」會利用雲端規模來識別您 App Service 應用程式上的攻擊並將焦點放在新興的攻擊上，在攻擊者處於偵察階段時，便進行掃描來識別多個裝載在 Azure 上之網站的弱點。 「資訊安全中心」會使用分析和機器學習模型來涵蓋所有允許客戶與其應用程式進行互動 (不論是透過 HTTP 還是透過管理方法) 的介面。 此外，作為 Azure 中的第一方服務，「資訊安全中心」也處於獨特的地位，負責提供涵蓋此 PaaS 之基礎計算節點的主機型安全性分析，讓「資訊安全中心」能夠偵測到對已遭惡意探索之 Web 應用程式的攻擊。

## <a name="prerequisites"></a>必要條件

若要監視及保護您的 App Service，您必須有一個與專屬機器相關的 App Service 方案。 這些方案包括：基本、標準、進階、隔離式或 Linux。 「Azure 資訊安全中心」不支援「免費」、「共用」或「使用量」方案。 如需詳細資訊，請參閱 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。

## <a name="security-center-protection"></a>資訊安全中心保護

「Azure 資訊安全中心」會保護您 App Service 執行所在的 VM 執行個體及管理介面。 它也會監視傳送給在 App Service 中執行之應用程式的要求和回應，以及從這些應用程式傳送的要求和回應。

「資訊安全中心」會與 App Service 原生整合，因此無須進行部署及上線 - 會完全在背景中完成整合。



## <a name="enabling-monitoring-and-protection-of-app-service"></a>啟用 App Service 監視和保護

1. 在 Azure 中，選擇 [資訊安全中心]。
2. 移至 [安全性原則]，然後選擇一個訂用帳戶。
3. 在訂用帳戶的資料列結尾，按一下 [編輯設定]。
4. 在 [定價層] 底下的 [應用程式服務] 資料列中，將方案切換至 [已啟用]。

![App Service 切換](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> 針對資源數量列出的執行個體數目代表您開啟定價層刀鋒視窗時，作用中的相關 App Service 執行個體數目。 由於此數目會依據您選取的調整規模選項變更，因此您需付費的執行個體數目也會相應地修改。

若要停用您 App Service 的監視和建議，請重複此程序並將 **App Service** 方案切換至 [已停用]。



## <a name="see-also"></a>請參閱
在本文中，您已來了解如何使用「Azure 資訊安全中心」的監視功能。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何在 Azure 資訊安全中心設定安全性設定。
* [管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理和回應安全性警示。
* [應用程式服務](security-center-virtual-machine-protection.md#app-services)：檢視所列出應用程式服務環境的健康情況摘要。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀態。
* [Azure 資訊安全中心常見問題](security-center-faq.md)：尋找有關使用服務的常見問題。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。
