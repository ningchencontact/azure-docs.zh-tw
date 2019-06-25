---
title: 包含檔案
description: 包含檔案
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174138"
---
1. 在 [方案總管]  中，以滑鼠右鍵按一下專案並選取 [發佈]  。

1. 在 **發佈**對話方塊中，選取**Microsoft Azure App Service**，選擇**建立新**，然後選取**發行**。

   ![挑選發佈目標](./media/webjobs-publish-netcore/pick-publish-target.png)

1. 在 **建立 App Service**對話方塊中，使用影像下方資料表中所指定的裝載設定：

    ![建立 App Service 對話方塊](./media/webjobs-publish-netcore/app-service-dialog.png)

    | 設定      | 建議值  | 描述                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以唯一識別新函式應用程式的名稱。 |
    | **訂用帳戶** | 選擇您的訂用帳戶 | 要使用的 Azure 訂用帳戶。 |
    | **[資源群組](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  要在其中建立函式應用程式的資源群組名稱。 選擇 [新增]  以建立新的資源群組。|
    | **[主控方案](../articles/app-service/overview-hosting-plans.md)** | App Service 方案 | [App Service 方案](../articles/app-service/overview-hosting-plans.md)會指定用來裝載應用程式的 Web 伺服器陣列位置、大小和功能。 在裝載多個應用程式時，您可以將 Web 應用程式設定為共用單一 App Service 方案來節省開支。 App Service 方案會定義區域、 執行個體大小、 級別計數和 SKU （免費、 共用、 Basic、 Standard 或 Premium）。 選擇**新增**來建立新的 App Service 方案。 |

1. 按一下 **建立**在 Azure 中建立 WebJob 和相關的資源，使用這些設定及部署您的專案程式碼。