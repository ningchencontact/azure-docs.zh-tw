---
title: 包含檔案
description: 包含檔案
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008496"
---
如果您打算繼續閱讀下一篇建議的文章，則可以保留您所建立的資源和組態，並加以重複使用。

否則，可以刪除您在本文中建立的本機組態和 Azure 資源，以避免產生費用。 

> [!IMPORTANT]
> 刪除 Azure 資源和資源群組是無法回復的動作。 當您刪除這些項目時，資源群組和其中包含的所有資源都將永久刪除。 請確定您不會誤刪錯誤的資源群組或資源。 如果您在現有的資源群組內建立了 IoT 中樞，而該群組中包含您想要保留的資源，則您只需刪除 IoT 中樞資源本身即可，而不要刪除資源群組。
>

若僅要刪除 IoT 中樞，請執行下列命令。 將 \<YourIoTHub> 取代為您的 IoT 中樞名稱，並將 \<TestResources> 取代為您的資源群組名稱：

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


若要依名稱刪除整個資源群組：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]。

2. 在 [依名稱篩選] 文字方塊中，輸入包含 IoT 中樞的資源群組名稱。 

3. 在結果清單中資源群組的右側，選取省略符號 (**...**)，然後選取 [刪除資源群組]。

    ![刪除資源群組](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. 系統將會要求您確認是否刪除資源群組。 重新輸入您資源群組的名稱以進行確認，然後選取 [刪除]。 片刻過後，系統便會刪除該資源群組及其所有內含的資源。






