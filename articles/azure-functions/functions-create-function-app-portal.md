---
title: Create a function app from the Azure Portal
description: Create a new function app in Azure from the portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230776"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>從 Azure 入口網站建立函數應用程式

This topic shows you how to use Azure Functions to create a function app in the Azure portal. 函數應用程式是裝載個別函式執行的容器。 

## <a name="create-a-function-app"></a>建立函數應用程式

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

當您建立函數應用程式時，請提供有效的**應用程式名稱**，其中只能包含字母、數字和連字號。 不允許使用底線 ( **_** ) 字元。

儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 儲存體帳戶名稱必須在 Azure 中是獨一無二的。 

函數應用程式建立之後，您可以使用一或多個不同的語言建立個別的函式。 使用[入口網站](functions-create-first-azure-function.md#create-function)、[連續部署](functions-continuous-deployment.md)或者[透過 FTP 上傳](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)來建立函式。

## <a name="service-plans"></a>服務方案

Azure Functions has three different service plans: Consumption plan, Premium plan, and Dedicated (App Service) plan. You must choose your service plan when your function app is created, and it cannot subsequently be changed. 如需詳細資訊，請參閱[選擇 Azure Functions 主控方案](functions-scale.md)。

If you are planning to run JavaScript functions on a Dedicated (App Service) plan, you should choose a plan with fewer cores. 如需詳細資訊，請參閱 [JavaScript 函式參考資料](functions-reference-node.md#choose-single-vcpu-app-service-plans)。

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>儲存體帳戶需求

When creating a function app, you must create or link to a general-purpose Azure Storage account that supports Blob, Queue, and Table storage. Azure Functions 會在內部使用「Azure 儲存體」來進行作業，例如管理觸發程序和記錄函式執行。 有些儲存體帳戶並不支援佇列和表格，例如僅限 Blob 的儲存體帳戶、Azure 進階儲存體和搭配 ZRS 複寫的一般用途儲存體帳戶。 建立函數應用程式時，[儲存體帳戶] 刀鋒視窗中會過濾掉這些帳戶。

>[!NOTE]
>當使用「使用情況主控方案」時，您的函式程式碼和繫結組態檔會儲存在主要儲存體帳戶中的 Azure 檔案儲存體中。 當您刪除主要儲存體帳戶時，會刪除此內容且無法復原。

若要深入了解儲存體帳戶類型，請參閱 [Azure 儲存體服務簡介](../storage/common/storage-introduction.md#azure-storage-services)。 

## <a name="next-steps"></a>後續步驟

雖然 Azure 入口網站可以讓建立及試用 Functions 變得容易，但是我們建議[本機開發](functions-develop-local.md)。 在入口網站中建立函數應用程式之後，您仍然必須新增函式。 

> [!div class="nextstepaction"]
> [新增由 HTTP 觸發的函式](functions-create-first-azure-function.md#create-function)
