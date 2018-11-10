---
title: Azure Machine Learning Studio 的新功能 | Microsoft Docs
description: Azure Machine Learning Studio 中可用的新功能。
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: 2f990ce30b66392e8a649e4e88b21457d9fe223b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212653"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio 的新功能

## <a name="october-2018"></a>2018 年 10 月

[執行 R 指令碼](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script)模組中的 R 語言引擎已新增 R 執行階段版本 - Microsoft R Open (MRO) 3.4.4。 MRO 3.4.4 以開放原始碼 CRAN R 3.4.4 作為基礎，因此可與適用於該 R 版本的套件相容。若要深入了解所支援的 R 套件，請參閱 [Azure Machine Learning studio 所支援的 R 套件](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List)一文。

## <a name="march-2017"></a>2017 年 3 月 
此 Microsoft Azure Machine Learning 更新版本提供下列功能：

* Azure Machine Learning BES 作業專用的容量

    Machine Learning 批次集區處理會使用 [Azure Batch](../../batch/batch-technical-overview.md) 服務，提供客戶管理的 Azure Machine Learning 批次執行服務級別。 批次集區處理可讓您建立 Azure Batch 集區，以便提交批次作業，並讓它們以預期的方式執行。

    如需詳細資訊，請參閱[適用於 Machine Learning 作業的 Azure Batch 服務](dedicated-capacity-for-bes-jobs.md)。


## <a name="august-2016"></a>2016 年 8 月 
此 Microsoft Azure Machine Learning 更新版本提供下列功能：
* 新的 [Microsoft Azure Machine Learning Web 服務](https://services.azureml.net/)入口網站可集中管理 Web 服務的所有層面，現在可以在此入口網站中管理傳統 Web 服務。    
  * 它提供 Web 服務 [使用量統計資料](manage-new-webservice.md)。
  * 使用範例資料來簡化測試 Azure Machine Learning 遠端要求呼叫。
  * 提供新的批次執行服務測試頁面，有範例資料和作業提交歷程記錄可用。
  * 提供更輕鬆的端點管理。

## <a name="july-2016"></a>2016 年 7 月 
此 Microsoft Azure Machine Learning 更新版本提供下列功能：
* 現在 Web 服務是透過 [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) 介面管理為 Azure 資源，有下列增強功能︰
  * 有新的 [REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx) 可部署和管理以 Resource Manager 為基礎的 Web 服務。
  * 新的 [Microsoft Azure Machine Learning Web 服務](https://services.azureml.net/)入口網站可集中管理 Web 服務的所有層面。
* 使用以 Resource Manager 為基礎的 API，運用 Web 服務的 Resource Manager 資源提供者 ，加入以新的訂用帳戶為基礎、多區域 Web 服務的部署模型。
* 使用新的 Resource Manager RP 進行計費，引入新的 [定價方案](https://azure.microsoft.com/pricing/details/machine-learning/) 和方案管理功能。
  * 您現在可以[將您的 Web 服務部署到多個區域](how-to-deploy-to-multiple-regions.md)而不需要在每個區域中建立訂用帳戶。
* 提供 Web 服務 [使用量統計資料](manage-new-webservice.md)。
* 使用範例資料來簡化測試 Azure Machine Learning 遠端要求呼叫。
* 提供新的批次執行服務測試頁面，有範例資料和作業提交歷程記錄可用。

此外，Machine Learning Studio 已更新，可讓您部署到新式 Web 服務模型，或繼續部署到傳統 Web 服務模型。 

