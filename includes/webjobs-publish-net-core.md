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
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751225"
---
1. 在 [方案總管] 中，以滑鼠右鍵按一下專案並選取 [發佈]。

1. 在“发布”对话框中，依次选择“Microsoft Azure 应用服务”和“新建”，然后选择“发布”。

   ![选取发布目标](./media/webjobs-publish-netcore/pick-publish-target.png)

1. 在“创建应用服务”对话框中，使用图片下方的表中指定的托管设置：

    ![建立 App Service 對話方塊](./media/webjobs-publish-netcore/app-service-dialog.png)

    | 設定      | 建議的值  | 描述                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **應用程式名稱** | 全域唯一的名稱 | 用以唯一識別新函式應用程式的名稱。 |
    | **訂用帳戶** | 選擇您的訂用帳戶 | 要使用的 Azure 訂用帳戶。 |
    | **[資源群組](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  要在其中建立函式應用程式的資源群組名稱。 選擇 [新增] 以建立新的資源群組。|
    | **[托管计划](../articles/app-service/overview-hosting-plans.md)** | App Service 方案 | [App Service 方案](../articles/app-service/overview-hosting-plans.md)會指定用來裝載應用程式的 Web 伺服器陣列位置、大小和功能。 在裝載多個應用程式時，您可以將 Web 應用程式設定為共用單一 App Service 方案來節省開支。 应用服务计划定义区域、实例大小、规模计数和 SKU（免费、共享、基本、标准或高级）。 然后，选择“新建”以创建一个新的应用服务计划。 |

1. 单击“创建”以使用这些设置在 Azure 中创建 Web 作业和相关资源，并部署项目代码。