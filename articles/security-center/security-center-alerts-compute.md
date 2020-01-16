---
title: Azure 資訊安全中心中的雲端原生運算威脅偵測 |Microsoft Docs
description: 本文提供 Azure 資訊安全中心中提供的雲端原生計算警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024866"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Azure 資訊安全中心中的雲端原生運算威脅偵測

做為原生解決方案，Azure 資訊安全中心對於跨多個目標的攻擊方法識別，有獨特的內部記錄可見度。 本文提供適用于下列 Azure 服務的警示：

* [Azure App Service](#app-services)
* [Azure 容器](#azure-containers) 

> [!NOTE]
> 這些服務目前無法在 Azure 政府和主權雲端區域中使用。

## Azure App Service<a name="app-services"></a>

資訊安全中心使用雲端的規模來識別以 App Service 執行之應用程式為目標的攻擊。 攻擊者會探查 web 應用程式，以找出併入侵弱點。 在路由傳送至特定環境之前，對 Azure 中執行之應用程式的要求會經歷數個閘道，並在其中進行檢查和記錄。 接著會使用此資料來識別入侵和攻擊者，並瞭解稍後將使用的新模式。

藉由使用 Azure 做為雲端提供者的可見度，資訊安全中心分析 App Service 內部記錄，以識別多個目標的攻擊方法。 例如，方法包含廣泛的掃描和分散式攻擊。 這種類型的攻擊通常來自于一小部分的 Ip，並顯示在多部主機上編目至類似端點的模式。 攻擊會搜尋易受攻擊的頁面或外掛程式，而無法從單一主機的觀點來識別。

如果您執行的是以 Windows 為基礎的 App Service 方案，資訊安全中心也可以存取基礎沙箱和 Vm。 除了上述的記錄資料，基礎結構也可以告訴故事，從攻擊者的新攻擊，到客戶電腦的危害。 因此，即使在惡意探索 web 應用程式之後部署資訊安全中心，它還是可能能夠偵測到進行中的攻擊。

如需 Azure App Service 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureappserv)。

## Azure 容器<a name="azure-containers"></a>

資訊安全中心為您的容器化環境提供即時威脅偵測，並產生可疑活動的警示。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

我們偵測到不同層級的威脅： 

* **主機層級**-資訊安全中心的代理程式（適用于標準層，如需詳細資訊，請參閱[定價](security-center-pricing.md)）監視 Linux 是否有可疑的活動。 代理程式會觸發源自節點或在其上執行之容器的可疑活動警示。 這類活動的範例包括 web 命令介面偵測，以及與已知可疑 IP 位址的連線。

    若要深入瞭解容器化環境的安全性，代理程式會監視容器特有的分析。 它會觸發事件的警示，例如建立許可權的容器、對 API 伺服器的可疑存取，以及在 Docker 容器內執行的安全殼層（SSH）伺服器。

    >[!NOTE]
    > 如果您選擇不在您的主機上安裝代理程式，您只會收到威脅偵測權益和警示的子集。 您仍然會收到與網路分析以及與惡意伺服器通訊相關的警示。

    如需主機層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-containerhost)。


* 針對**AKS 叢集層級**，會根據 Kubernetes audit logs 分析來監視威脅偵測。 若要啟用此**無代理**程式監視，請從 [**定價 & 設定**] 頁面將 [Kubernetes] 選項新增至您的訂用帳戶（請參閱[定價](security-center-pricing.md)）。 若要在此層級產生警示，資訊安全中心使用 AKS 所取出的記錄來監視 AKS 管理的服務。 此層級的事件範例包括公開的 Kubernetes 儀表板、建立高特殊許可權的角色，以及建立敏感性裝載。

    >[!NOTE]
    > 資訊安全中心會針對在訂用帳戶設定上啟用 Kubernetes 選項後發生的 Azure Kubernetes Service 動作和部署產生偵測警示。 

    如需 AKS 叢集層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-akscluster)。

此外，我們的全球安全性研究人員小組會持續監視威脅的範圍。 他們會在探索到容器特有的警示和弱點時加以新增。

## <a name="next-steps"></a>後續步驟

* 如需 App Service 計畫的詳細資訊，請參閱[App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。