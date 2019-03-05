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
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885120"
---
1. 若要建立新的應用程式設定存放區，請先登入 [Azure 入口網站](https://aka.ms/azconfig/portal)。 在頁面的左上方，按一下 [+ 建立資源]。 在 [搜尋 Marketplace] 文字方塊中，輸入**應用程式設定**，然後按 **Enter**。

    ![搜尋應用程式設定](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. 從搜尋結果按一下 [應用程式設定]，然後按一下 [建立]。

3. 在 [應用程式設定] > [建立] 頁面中，輸入下列設定：

    | 設定 | 建議的值 | 說明 |
    |---|---|---|
    | **資源名稱** | 全域唯一的名稱 | 輸入要對應用程式設定存放區資源使用的唯一資源名稱。 名稱必須是介於 1 到 63 個字元的字串，而且只能包含數字、字母和 `-` 字元。 名稱的開頭或結尾不能是 `-` 字元，且連續的 `-` 字元無效。  |
    | **訂用帳戶** | 您的訂用帳戶 | 選取您要用來測試應用程式設定的 Azure 訂用帳戶。 如果您的帳戶僅有一個訂用帳戶，則會自動加以選取，而且不會顯示 [訂用帳戶] 下拉式清單。 |
    | **資源群組** | *AppConfigTestResources* | 為應用程式設定存放區資源選取或建立資源群組。 此群組可用於組織多個資源，以便能夠藉由刪除資源群組來同時刪除多個資源。 如需詳細資訊，請參閱[使用資源群組管理您的 Azure 資源](/azure/azure-resource-manager/resource-group-overview)。 |
    | **位置** | *美國中部* | 使用 [位置] 來指定裝載應用程式設定存放區所在的地理位置。 為獲得最佳效能，建議您在與應用程式其他元件相同的區域中建立資源。 |

    ![建立應用程式設定存放區資源](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. 按一下頁面底部的 [新增] 。 部署需要幾分鐘的時間才能完成。

5. 部署完成後，按一下 [設定] > [存取索引鍵]。 記下主要唯讀或主要讀寫索引鍵連接字串。 稍後，您會使用此連接字串來設定您的應用程式，使其與剛才建立的應用程式設定存放區進行通訊。

6. 按一下 [索引鍵/值總管] 和 [+ 建立] 來新增下列索引鍵-值組：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:BackgroundColor | 白色 |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | 黑色 |
    | TestApp:Settings:Message | Azure 應用程式設定的值 |

    您目前會先讓 [標籤] 和 [內容類型] 保持空白。
