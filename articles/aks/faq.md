---
title: "Azure Container Service 的常見問題集"
description: "提供一些關於 Azure Container Service 常見問題的解答。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 2b78479c257930669729a7781b3893b3e2064bab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>關於 Azure Container Service (AKS) 的常見問題集

本文將說明關於 Azure Container Service (AKS) 的常見問題。

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>哪些 Azure 區域會有 Azure Container Service (AKS)？ 

- 加拿大中部 
- 加拿大東部 
- 美國中部 
- 美國東部 
- 東南亞 
- 西歐 
- 美國西部 2 

## <a name="when-will-additional-regions-be-added"></a>何時會新增其他區域？ 

需求增加時，便會新增其他區域。

## <a name="are-security-updates-applied-to-aks-nodes"></a>是否會將安全性更新套用至 AKS 節點？ 

我們會排程在夜間將 OS 安全性修補程式套用至叢集中的節點，但不會執行重新啟動。 如有需要，您可以透過入口網站或 Azure CLI 重新啟動節點。 升級叢集時會使用最新的 Ubuntu 映像，並套用所有安全性修補程式 (而且會重新啟動)。

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>是否建議客戶使用 ACS 或 AKS？ 

有鑑於 Azure Container Service (AKS) 將會在日後正式運作，建議您在 AKS 建置 PoC、開發和測試叢集，但在 ACS-Kubernetes 建置生產叢集。  

## <a name="when-will-acs-be-deprecated"></a>何時會取代 ACS？ 

大約會在 AKS 正式運作時取代 ACS。 在該日期之後，您會有 12 個月的時間可將叢集移轉至 AKS。 在這 12 個月的期間，您可以執行所有 ACS 作業。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支援節點自動調整？ 

不支援節點自動調整，但目前正在規劃。 您可以看一下這個開放原始碼架構的[自動調整實作][auto-scaler]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>為何會使用 AKS 建立兩個資源群組？ 

第二個資源群組是系統自動建立的，以方便您刪除所有與 AKS 部署相關聯的資源。

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault 是否會與 AKS 整合？ 

不會，但已在規劃這項整合。 於此同時，您可以嘗試 [Hexadite][hexadite] 所提供的下列解決方案。 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  