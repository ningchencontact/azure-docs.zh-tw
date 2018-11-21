---
title: 如何升級適用於容器的 Azure 監視器 (預覽) | Microsoft Docs
description: 本文說明如何升級適用於容器的 Azure 監視器所使用的 Log Analytics 代理程式。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: fb45002d284bc28dfb7093f69cfac7aae0681e8d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628368"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>如何升級適用於容器的 Azure 監視器 (預覽) 代理程式
適用於容器的 Azure 監視器會使用適用於 Linux 的 Log Analytics 代理程式容器化版本。 代理程式的新版本發行時，代理程式並不會在您裝載於 Azure Kubernetes Service (AKS) 中的受控 Kubernetes 叢集上自動升級。

本文說明升級代理程式的程序。

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>在受監視的 Kubernetes 叢集上升級代理程式
升級代理程式的程序由兩個直接簡單的步驟所組成。 第一個步驟是使用 Azure CLI 停用適用於容器的 Azure 監視器所進行的監視。  請依照[停用監視](monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli)一文中說明的步驟操作。 我們可以使用 Azure CLI 從叢集中的節點移除代理程式，而不會影響解決方案和儲存在工作區中的對應資料。 

>[!NOTE]
>當您執行這項維護活動時，叢集中的節點將不會轉送收集的資料，且在您移除代理程式到安裝新版本的這段時間內，效能檢視不會顯示資料。 
>

若要安裝新版的代理程式，請依照[使監視上線](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli)一文中說明的步驟，使用 Azure CLI 完成此程序。  

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的更新健康情況計量。 

## <a name="next-steps"></a>後續步驟
如果您在升級代理程式時遇到問題，請檢閱[疑難排解指南](monitoring-container-insights-troubleshoot.md)以取得支援。