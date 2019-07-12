---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836770"
---
## <a name="what-happens-to-my-app-during-deployment"></a>在部署期間我的應用程式會發生什麼事？

所有正式支援的部署方法中的檔案進行變更`/home/site/wwwroot`應用程式的資料夾。 這些檔案是相同的在生產環境中執行。 因此，部署可能會由於鎖定檔案失敗。 在生產環境中的應用程式可能也不正常的行為在部署期間，因為並非所有的檔案更新一次。 有幾種不同的方式可以避免這些問題：

- 停止您的應用程式，或在部署期間針對您的應用程式啟用離線模式。 如需詳細資訊，請參閱 <<c0> [ 應付鎖定的檔案，在部署期間](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)。
- 部署到[預備位置](../articles/app-service/deploy-staging-slots.md)，並且啟用[自動交換](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
- 使用[執行從封裝](https://github.com/Azure/app-service-announcements/issues/84)而不是連續部署。
