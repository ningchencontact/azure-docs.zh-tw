---
title: 自訂語音-語音服務的部署模型
titlesuffix: Azure Cognitive Services
description: 在本文件中，您將學習如何建立和部署使用自訂語音入口網站的端點。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f2b69bf3e5d536e7e813ef4a7c36b2937141fd43
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606616"
---
# <a name="deploy-a-custom-model"></a>部署自訂模型

您已上傳和檢查資料、 評估精確度，以及訓練自訂模型之後，您可以部署自訂的端點，以使用您的應用程式、 工具和產品。 在本文件中，您將學習如何建立和部署使用自訂語音入口網站的端點。

## <a name="create-a-custom-endpoint"></a>建立自訂端點

若要建立新的自訂端點，請選取**部署**從頁面頂端的客製化語音功能表。 如果這是您第一次執行時，您會發現沒有列在資料表端點。 您已建立端點之後，您將使用此頁面來追蹤每個已部署的端點。

接下來，選取**新增端點**，然後輸入**名稱**並**描述**適用於您的自訂端點。 然後選取您想要與這個端點關聯的自訂模型。 從這個頁面上，您也可以啟用記錄。 記錄可讓您監視端點的流量。 如果停用，則不會儲存流量。

![如何將模型部署](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 別忘了接受使用條款及定價詳細資料。

接下來，選取 [建立]  。 此動作會將您返回**部署**頁面。 資料表現在包含項目對應至您的自訂端點。 端點的狀態會顯示目前的狀態。 可能需要 30 分鐘的時間來具現化新的端點，使用您的自訂模型。 當部署的狀態會變為**完成**，端點可供使用。

部署您的端點之後，將端點名稱會顯示為連結。 按一下連結以顯示您的端點，例如端點索引鍵、 端點 URL 和範例程式碼的特定資訊。

## <a name="view-logging-data"></a>檢視記錄資料

記錄資料可供下載**端點 > 詳細資料**。

## <a name="next-steps"></a>後續步驟

* 使用您的自訂端點與[語音 SDK](speech-sdk.md)

## <a name="additional-resources"></a>其他資源

* [準備和測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [訓練您的模型](how-to-custom-speech-train-model.md)
* [將模型部署](how-to-custom-speech-deploy-model.md)
