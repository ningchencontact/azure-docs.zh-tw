---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751575"
---
## <a name="what-happens-to-my-app-during-deployment"></a>在部署期間我的應用程式會發生什麼事？

所有正式支援的部署方法都會對應用程式的 `/home/site/wwwroot` 資料夾中的檔案進行變更。 這些檔案是用來執行您的應用程式。 因此，部署可能會因為鎖定的檔案而失敗。 應用程式在部署期間可能也會無法預期的行為，因為並非所有檔案都同時更新。 對於客戶面向的應用程式，這是不必要的。 有幾種不同的方式可以避免這些問題：

- 停止您的應用程式，或在部署期間針對您的應用程式啟用離線模式。 如需詳細資訊，請參閱[在部署期間處理鎖定的](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)檔案。
- 部署到[預備位置](../articles/app-service/deploy-staging-slots.md)，並且啟用[自動交換](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
- 使用 [[從封裝執行](https://github.com/Azure/app-service-announcements/issues/84)]，而不是連續部署。
