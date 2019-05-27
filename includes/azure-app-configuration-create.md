---
title: 包含檔案
description: 包含檔案
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: 9b86f2e05e2cb42470061bd6398b4200607f2418
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133046"
---
1. 若要建立新的應用程式設定存放區，請登入 [Azure 入口網站](https://aka.ms/azconfig/portal)。 在頁面的左上角，選取 [+ 建立資源]。 在 [搜尋 Marketplace] 方塊中，輸入**應用程式設定**，然後按 Enter。

    ![搜尋應用程式設定](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. 從搜尋結果中選取 [應用程式設定]，然後選取 [建立]。

3. 在 [應用程式設定]  >  [建立] 頁面上，輸入下列設定。

    | 設定 | 建議的值 | 說明 |
    |---|---|---|
    | **資源名稱** | 全域唯一的名稱 | 輸入要對應用程式設定存放區資源使用的唯一資源名稱。 名稱必須是介於 1 到 63 個字元的字串，而且只能包含數字、字母和 `-` 字元。 名稱的開頭或結尾不能是 `-` 字元，且連續的 `-` 字元無效。  |
    | **訂用帳戶** | 您的訂用帳戶 | 選取您要用來測試應用程式設定的 Azure 訂用帳戶。 如果您的帳戶僅有一個訂用帳戶，則會自動加以選取，而且不會顯示 [訂用帳戶] 下拉式清單。 |
    | **資源群組** | *AppConfigTestResources* | 為應用程式設定存放區資源選取或建立資源群組。 此群組可用於組織多個資源，以便能夠藉由刪除資源群組來同時刪除多個資源。 如需詳細資訊，請參閱[使用資源群組管理您的 Azure 資源](/azure/azure-resource-manager/resource-group-overview)。 |
    | **位置** | *美國中部* | 使用 [位置] 來指定裝載應用程式設定存放區所在的地理位置。 為了獲得最佳效能，請將資源建立在與應用程式其他元件相同的區域內。 |

    ![建立應用程式設定存放區資源](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. 選取 [建立] 。 部署可能需要幾分鐘的時間才能完成。

5. 完成部署之後，請選取 [設定]  >  [存取金鑰]。 記下主要唯讀或主要讀寫索引鍵連接字串。 稍後，您會使用此連接字串來設定您的應用程式，使其與您建立的應用程式設定存放區進行通訊。
