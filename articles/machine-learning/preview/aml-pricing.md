---
title: Azure Machine Learning 服務的定價和計費
description: 本文包含 Azure Machine Learning 預覽版功能的定價和計費常見問題
services: machine-learning
author: j-martens
ms.author: jmartens
manager: cgronlund
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 5e057f3fe4a4ff06e0acac29b3dcd11fa901fc40
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="pricing-and-billing-for-azure-machine-learning-services"></a>Azure Machine Learning 服務的定價和計費

如需完整詳細資料，或要檢視範例帳單，請造訪 [Azure 定價頁面](https://azure.microsoft.com/pricing/details/machine-learning-services/)。  

以下是一些關於定價和計費的常見問題。

## <a name="can-i-use-azure-machine-learning-for-free-during-preview"></a>在預覽期間，我可以免費使用 Azure Machine Learning 嗎？    

Azure Machine Learning Workbench 應用程式可免費提供給 Azure 訂閱者使用。

「測試服務」和「模型管理」除了付費層 (在「公開預覽版」期間享有折扣) 之外，也提供免費層。

## <a name="am-i-charged-based-on-how-many-experiments-i-run"></a>是否會依照我執行測試的次數向我收費？

否，「測試服務」可讓您視需要執行許多測試，而且只依使用者數目收費。 測試計算資源會另外收費。  建議您執行多次測試，以便找出最適合您解決方案的模型。 

## <a name="am-i-charged-based-on-how-many-times-my-web-services-is-called"></a>是否會依照呼叫 Web 服務的次數向我收費？

編號 您可以視需要經常呼叫 Web 服務，而不會涉及「模型管理」計費。 您可以完全掌控要如何調整部署，以符合您應用程式的需求。

## <a name="how-can-i-scale-the-units-i-purchase-in-the-azure-machine-learning-model-management"></a>我要如何調整在 Azure Machine Learning 模型管理中所購買的單位數？

您可以使用 Azure 入口網站或 CLI 來變更單位數 (增加或減少)。 

## <a name="what-does-a-bill-look-like"></a>帳單的外觀為何？

每天會產生帳單。 為了計費目的，一天的開始為 UTC 午夜。 每月會產生帳單。 針對與 Azure Machine Learning 一起取用的所有 Azure 服務，會產生個別的費用。 這些費用包含但不限於： 
- 計算費用
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob 儲存體
- Application Insights
- Azure 金鑰保存庫
- Visual Studio Team Services
- Azure 事件中樞
- Azure 串流分析 - 如需進一步的詳細資料，或若要檢視範例帳單，請造訪 [Azure 定價頁面](https://azure.microsoft.com/pricing/details/machine-learning-services/)。 
