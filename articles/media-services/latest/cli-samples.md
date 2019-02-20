---
title: Azure CLI 範例 - Azure 媒體服務 | Microsoft Docs
description: 「Azure 媒體服務」服務的 Azure CLI 範例
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/11/2019
ms.author: juliako
ms.openlocfilehash: 919dbd057f2fa1d762775ed799505796a420864a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109243"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure 媒體服務的 Azure CLI 範例

下表包含「Azure 媒體服務」的 Azure CLI 範例連結。

## <a name="examples"></a>範例

|  |  |
|---|---|
|**調整**||
| [調整媒體保留單元](media-reserved-units-cli-how-to.md)|針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 MRU 佈建帳戶。 <br/>指令碼示範如何使用 CLI 來調整媒體保留單元 (MRU)。|
|**帳戶**||
| [建立媒體服務帳戶](./scripts/cli-create-account.md) | 建立 Azure 媒體服務帳戶。 此外，建立一個服務主體，並用它來存取 API，然後以程式設計方式管理該帳戶。 |
| [重設帳戶認證](./scripts/cli-reset-account-credentials.md)|重設帳戶認證，並還原為 app.config 設定。|
|**資產**||
| [建立資產](./scripts/cli-create-asset.md)|建立媒體服務資產以供上傳內容。|
| [上傳檔案](./scripts/cli-upload-file-asset.md)|將本機檔案上傳至儲存體容器。|
| **轉換**與**作業**||
| [建立轉換](./scripts/cli-create-transform.md)|示範如何建立轉換。 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。<br/> 請一律要檢查是否已有所需名稱和「配方」的轉換。 若有，請予以重複使用。 |
| [建立作業](./scripts/cli-create-jobs.md)|使用 HTTPs URL 提交作業給簡單的編碼轉換。|
| [建立 EventGrid](./scripts/cli-create-event-grid.md)|針對作業狀態變更建立帳戶層級的事件方格訂用帳戶。|
| **傳遞**||
| [發佈資產](./scripts/cli-publish-asset.md)| 建立串流定位器並取回串流 URL。 |
| [Filter](filters-dynamic-manifest-cli-howto.md)| 設定影片隨選資產的篩選條件，並示範如何使用 CLI 以建立[帳戶篩選條件](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)和[資產篩選條件](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)。 

## <a name="see-also"></a>另請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
