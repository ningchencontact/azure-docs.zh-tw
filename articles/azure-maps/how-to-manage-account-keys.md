---
title: 管理 Azure 地圖服務帳戶和金鑰 | Microsoft Docs
description: 您可以使用 Azure 入口網站來管理 Azure 地圖服務帳戶以及管理存取金鑰。
author: walsehgal
ms.author: v-musehg
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 81746279e935f9fa5b6ef00bc8d31e75f4d3bd20
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103039"
---
# <a name="manage-your-azure-maps-account-and-keys"></a>管理 Azure 地圖服務帳戶和金鑰

您可以透過 Azure 入口網站管理 Azure 地圖服務帳戶和金鑰。 在取得帳戶和金鑰後，您即可在網站或行動裝置應用程式中實作 API。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-a-new-account"></a>建立新帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 Azure 入口網站中左上角的 [建立資源]。

3. 搜尋並選取 [地圖服務]。 然後選取 [建立]。

4. 輸入新帳戶的資訊。

![在入口網站中輸入帳戶資訊](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>在帳戶頁面上管理金鑰

建立帳戶後，您會獲得兩個隨機產生的金鑰。 若要擷取地圖資料或建立新的 JavaScript 地圖執行個體，請使用這兩個金鑰對 Azure 地圖服務 API 進行驗證。

您可以在 Azure 入口網站中找到金鑰。 瀏覽至您的帳戶。 然後，從功能表中選取 [金鑰]。

![在入口網站中管理帳戶金鑰](./media/how-to-manage-account-keys/account-keys-portal.png)

在此頁面中，您可以複製金鑰或產生新的金鑰。

## <a name="delete-an-account"></a>刪除帳戶

您可以從 Azure 入口網站刪除帳戶。 瀏覽至帳戶概觀頁面，然後選取 [刪除]。

![在入口網站中刪除您的帳戶](./media/how-to-manage-account-keys/account-delete-portal.png)

接著，您會看到確認頁面。 您可以藉由鍵入帳戶名稱來確認刪除該帳戶。

## <a name="next-steps"></a>後續步驟

* 了解如何管理 Azure 地圖服務帳戶的定價層：
    > [!div class="nextstepaction"] 
    > [管理定價層](./how-to-manage-pricing-tier.md)

* 了解如何查看 Azure 地圖服務帳戶的 API 使用計量：
    > [!div class="nextstepaction"] 
    > [檢視使用計量](./how-to-view-api-usage.md)
