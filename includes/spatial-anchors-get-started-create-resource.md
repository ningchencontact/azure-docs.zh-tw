---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752612"
---
## <a name="create-a-spatial-anchors-resource"></a>建立 Spatial Anchors 資源

1. 瀏覽至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。

2. 在 Azure 入口網站的左側功能表中，選取 [建立資源]。

3. 在搜尋列中搜尋 "Spatial Anchors"。

   ![搜尋 Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. 選取 [Spatial Anchors (預覽)] 以開啟對話方塊，然後選取 [建立]。

5. 在 [Spatial Anchors 帳戶] 表單中：

   1. 指定唯一的資源名稱。
   2. 選取要連結資源的訂用帳戶。
   3. 選取 [新建] 以建立資源群組，並將資源群組命名為 **myResourceGroup**，然後選取 [確定]。
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. 選取將放置此資源的位置 (區域)。
   5. 選取 [新增] 開始建立資源。

   ![建立資源](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. 資源成功建立之後，您可以檢視資源屬性。 請記下資源的 [帳戶識別碼] 值，因為後續將需要此值。

   ![檢視資源屬性](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. 在 [設定] 下方選取 [金鑰]，並記下 [主要金鑰] 值。 此值是 `Account Key`，稍後將會用到。

   ![檢視帳戶金鑰](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
