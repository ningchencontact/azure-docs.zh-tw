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
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840625"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure 媒體服務的 Azure CLI 範例

下表包含「Azure 媒體服務」的 Azure CLI 範例連結。

## <a name="examples"></a>範例

|  |  |
|---|---|
|**調整**||
| [調整媒體保留單元](media-reserved-units-cli-how-to.md)|針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 MRU 佈建帳戶。 <br/>指令碼示範如何使用 CLI 來調整媒體保留單元 (MRU)。|
|**帳戶**||
| [建立媒體服務帳戶](create-account-cli-how-to.md) | 指令碼會建立 Azure 媒體服務帳戶。 |
| [重設帳戶認證](./scripts/cli-reset-account-credentials.md)|重設帳戶認證，並還原為 app.config 設定。|
|**資產**||
| [建立資產](./scripts/cli-create-asset.md)|建立媒體服務資產以供上傳內容。|
| [上傳檔案](./scripts/cli-upload-file-asset.md)|將本機檔案上傳至儲存體容器。|
| **轉換**與**作業**||
| [建立轉換](./scripts/cli-create-transform.md)|示範如何建立轉換。 轉換會說明用於處理視訊或音訊檔案的簡單工作流程 (通常稱為「配方」)。<br/> 您應該一律會先檢查轉換所需的名稱，且 「 配方 」 存在。 若有，請予以重複使用。 |
| [使用自訂的轉換進行編碼](custom-preset-cli-howto.md) | 示範如何建置自訂預設值為目標的特定案例或裝置需求。|
| [建立作業](./scripts/cli-create-jobs.md)|使用 HTTPs URL 提交作業給簡單的編碼轉換。|
| [建立 EventGrid](./scripts/cli-create-event-grid.md)|針對作業狀態變更建立帳戶層級的事件方格訂用帳戶。|
| **傳遞**||
| [發佈資產](./scripts/cli-publish-asset.md)| 建立串流定位器並取回串流 URL。 |
| [Filter](filters-dynamic-manifest-cli-howto.md)| 設定影片隨選資產的篩選條件，並示範如何使用 CLI 以建立[帳戶篩選條件](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)和[資產篩選條件](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)。 

## <a name="see-also"></a>請參閱

- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [快速入門：Stream 視訊檔案-CLI](stream-files-cli-quickstart.md)
