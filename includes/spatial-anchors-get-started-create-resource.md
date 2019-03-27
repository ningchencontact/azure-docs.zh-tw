---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305297"
---
## <a name="create-a-spatial-anchors-resource"></a>建立 Spatial Anchors 資源

移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。

在 Azure 入口網站的左側導覽窗格中，選取 [建立資源]。

使用搜尋方塊搜尋 **Spatial Anchors**。

   ![搜尋 Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

選取 [Spatial Anchors]。 在對話方塊中，選取 [建立]。

在 [Spatial Anchors 帳戶] 對話方塊中：

- 輸入唯一的資源名稱。
- 選取您要連結資源的訂用帳戶。
- 選取 [新建] 來建立資源群組。 將其命名為 **myResourceGroup**，然後選取 [確定]。
      [!INCLUDE [resource group intro text](resource-group.md)]
- 選取要在其中放置資源的位置 (區域)。
- 選取 [新增] 開始建立資源。

   ![建立資源](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

建立資源之後，您可以檢視資源屬性。 將資源的 [帳戶識別碼] 值複製到文字編輯器，因為您稍後需要用到它。

   ![資源屬性](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

在 [設定] 底下，選取 [金鑰]。 將 [主要金鑰] 值複製到文字編輯器中。 此值為 `Account Key`。 稍後您將會用到此資訊。

   ![帳戶金鑰](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
